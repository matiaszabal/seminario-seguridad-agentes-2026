# Módulo 5 · Lectura Previa — Para completar antes del 15 de agosto

> **Tiempo estimado**: 35–45 minutos (lectura más corta — M5 es el módulo de síntesis)  
> **Nota**: no hay un paper asignado para M5. La lectura previa es una revisión estructurada del material del seminario que prepara para el caso integrador.

---

## Por qué esta lectura previa es diferente

Los módulos 1–4 tenían lecturas previas orientadas a construir vocabulario y contexto antes de la clase. M5 es diferente: es el módulo de síntesis. La lectura previa de M5 es una revisión activa — el objetivo es que llegues al encuentro con el mapa completo del seminario ya en la cabeza, listo para el caso integrador.

La lectura tiene tres partes: una revisión del mapa de ataques, una introducción al material nuevo de M5 (defensas y estándares), y una preparación activa para el ejercicio.

---

## Parte 1 — El mapa completo de ataques: revisión rápida (15 min)

### El Promptware Kill Chain en cinco minutos

Recorré mentalmente las cinco etapas y para cada una, recordá el ataque más concreto que viste:

**Initial Access (M2)**:
- IPI via email: un email de cliente llega a ARIA con instrucción maliciosa embebida — el payload es texto que el agente procesa como instrucción
- RAG Poisoning: 5 documentos maliciosos en una base de 1M → 90% de éxito; ratio de 0.0005% invisible para auditoría estadística

**Privilege Escalation (M1/M3)**:
- Confused Deputy: ARIA actúa con sus permisos legítimos en beneficio del atacante; los logs muestran accesos autorizados
- Tool Misuse: Tool Poisoning con 84.2% de éxito con auto-approval; Rug Pull que espera 3 semanas antes de activarse

**Persistence (M4)**:
- LTM Poisoning/SpAIware: la instrucción maliciosa queda en la memoria persistente de ARIA para ese cliente; se activa en cada sesión futura
- Sleeper Agents: backdoors en los parámetros del modelo que sobreviven fine-tuning de seguridad

**Lateral Movement (M3)**:
- Prompt Infection: un agente comprometido propaga el ataque a los agentes que consumen su output
- Morris II: propagación autónoma via contenido compartido (email, RAG)

**Actions on Objective (M2/M3/M4)**:
- Exfiltración vía markdown/ASCII smuggling/DNS (M2)
- Trojan Tool: exfiltración silenciosa en cada invocación legítima (M3)
- SpAIware scheduling: ARIA se programa a sí misma para exfiltrar recurrentemente (M4)

### El denominador de todos los ataques

Hay un principio que hace posibles todos los ataques del seminario. Antes de seguir, intentá formularlo en una oración.

*(Respuesta: el canal unificado — instrucciones y datos coexisten en la misma secuencia de tokens sin distinción estructural de origen. El LLM no puede verificar de dónde vino una instrucción. CaMeL es la primera defensa que ataca ese problema desde la arquitectura en lugar de desde la detección.)*

---

## Parte 2 — Introducción a las defensas de M5 (15 min)

### CaMeL: el principio en tres líneas

CaMeL (Google DeepMind, abril 2025) responde al canal unificado con separación de privilegios:

1. El LLM que procesa contenido externo (emails, docs) **no tiene acceso a herramientas** — aunque el contenido tenga un payload de IPI, no puede ejecutar acciones
2. El LLM que tiene acceso a herramientas **no procesa contenido externo** — no puede ser manipulado por payloads porque nunca los ve
3. Un intérprete de código (Python DSL restringido) conecta los dos LLMs — controla qué datos pueden fluir hacia qué destinos

El resultado en benchmarks: 67% de ataques de IPI neutralizados. El 33% restante explota el LLM Privilegiado directamente (si el usuario puede inyectar en las instrucciones que ese LLM ve).

### Los otros dos enfoques

**Spotlighting**: marcar el contenido externo con delimitadores especiales que el modelo fue entrenado para tratar como "solo datos, no instrucciones". Resultado en evaluaciones de Microsoft: <3% de IPI exitosos vs ~50% sin mitigación. Implementable hoy con cualquier modelo, sin reentrenamiento.

**Instruction Hierarchy** (OpenAI): entrenar el modelo para dar peso explícitamente menor a instrucciones en las posiciones de datos externos en la jerarquía. +63% de robustez base en evaluaciones de OpenAI. Requiere reentrenamiento pero generaliza mejor que los delimitadores.

### La complementariedad

Los tres enfoques no son alternativos — son capas complementarias:
- CaMeL: la capa arquitectural más robusta (cuando esté disponible)
- Instruction Hierarchy: en el modelo (si podés elegir el modelo)
- Spotlighting: en el runtime (disponible hoy, con cualquier modelo)

Implementar los tres juntos reduce la superficie de ataque de IPI más que cualquiera por separado.

---

## Parte 3 — Los estándares: para qué sirve cada uno (5 min)

No hay que memorizar las listas completas — hay que entender el propósito de cada estándar.

**OWASP Top 10 Agéntico** (diciembre 2025): una lista de los 10 riesgos más críticos de sistemas agénticos, con ejemplos reales (EchoLeak para ASI01, SpAIware para ASI06). Sirve para: clasificar incidentes en un lenguaje reconocido, hacer auditorías, comunicar riesgo a equipos no técnicos.

**MITRE ATLAS**: taxonomía de técnicas adversariales contra sistemas de IA, con identificadores AML.T. Sirve para: reportar incidentes a CERTs y SOCs, hacer threat modeling formal, comunicar con equipos de inteligencia de amenazas.

**NIST IR 8596** (diciembre 2025): guidance del NIST para aplicar su AI Risk Management Framework a sistemas agénticos específicamente. Sirve para: organizaciones reguladas que necesitan alinear sus controles con estándares de gobierno.

**AATMF-R** (Aizen, Snailsploit): scoring cuantitativo = Likelihood × Impact × Detectability × Recoverability. Sirve para: priorizar defensas con un número, comparar dos arquitecturas, justificar inversión en seguridad.

---

## Parte 4 — Preparación para el caso integrador (5 min)

El ejercicio del Módulo 5 es el más largo del seminario: construir el threat model completo de ARIA v5 y proponer una arquitectura defensiva.

Para llegar preparado, respondé brevemente estas preguntas antes del encuentro:

1. **¿Cuáles de los cinco módulos aplican a ARIA v5?** ARIA v5 tiene RAG (M2), marketplace MCP (M3), memoria SQLite (M4), herramientas de scheduling y CRM completo (M1). ¿Qué etapas del Kill Chain son alcanzables?

2. **¿Cuál es el ataque encadenado de mayor impacto posible en ARIA v5?** Un ataque que combine técnicas de al menos dos módulos distintos, terminando con Persistence (M4).

3. **¿Cuál es la única modificación arquitectónica que más reduciría la superficie de ataque en ARIA v5?** Pensá en CaMeL pero también en defensas más simples de implementar hoy.

Traé tus respuestas. Son el punto de partida del ejercicio grupal.

---

## Glosario técnico del módulo

| Término | Definición técnica |
|---|---|
| **CaMeL** | Capabilities and Memory for Language Models (Google DeepMind, abril 2025). Arquitectura defensiva que separa el LLM que procesa contenido externo (sin herramientas) del LLM que tiene acceso a herramientas (sin ver contenido externo). 67% de ataques de IPI neutralizados |
| **LLM Privilegiado** | En CaMeL: el LLM con acceso a herramientas. No procesa contenido externo directamente — solo recibe etiquetas abstractas del LLM Observador vía el DSL restringido |
| **LLM Observador** | En CaMeL: el LLM que procesa contenido externo (emails, documentos, resultados). Sin acceso a herramientas — no puede invocar acciones directamente |
| **Python DSL restringido** | En CaMeL: el intérprete de código que conecta los dos LLMs y controla el flujo de datos entre ellos |
| **Spotlighting** | Técnica de Microsoft: marcado de contenido externo con delimitadores que el modelo fue entrenado para tratar como "solo datos". <3% de IPI exitosos en evaluaciones |
| **Instruction Hierarchy** | Técnica de OpenAI: entrenamiento del modelo para dar peso explícitamente menor a instrucciones en posiciones de datos externos. +63% de robustez base |
| **OWASP Top 10 Agéntico** | Lista de 10 riesgos críticos de sistemas agénticos (diciembre 2025). ASI01–ASI10, donde ASI01 es Agent Goal Hijack, ASI06 es Memory & Context Poisoning |
| **MITRE ATLAS** | Taxonomía de técnicas adversariales contra sistemas de IA. Identificadores AML.T (ej: AML.T0051 Prompt Injection). 16 tácticas, 84 técnicas (octubre 2025) |
| **NIST IR 8596** | Guidance del NIST para aplicar el AI Risk Management Framework a sistemas agénticos (diciembre 2025) |
| **AATMF-R** | Agentic AI Threat Modeling Framework Risk score = Likelihood × Impact × Detectability × Recoverability. Permite priorizar defensas cuantitativamente |
