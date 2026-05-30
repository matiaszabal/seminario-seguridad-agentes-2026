# Módulo 5 · Guía Docente — Arquitecturas Defensivas y Gobernanza

> **Uso**: Documento interno del instructor. No distribuir a alumnos.  
> **Fecha**: Sábado 15 de agosto de 2026 (último encuentro del seminario)

---

## Objetivo pedagógico del módulo

Al finalizar, el participante debe poder:

1. Explicar la arquitectura de CaMeL y por qué su defensa es más robusta que los enfoques basados en filtros: el principio de separación de LLM privilegiado / LLM observador, y la consecuencia de que el LLM que procesa contenido no confiable no tiene acceso a herramientas
2. Comparar las tres defensas técnicas principales (CaMeL, Spotlighting, Instruction Hierarchy) en términos de mecanismo, cobertura, implementabilidad y complementariedad
3. Usar el OWASP Top 10 Agéntico (ASI01–ASI10) para clasificar correctamente los vectores de ataque de M2–M4 y mapearlos a MITRE ATLAS con identificadores AML.T
4. Producir un threat model estructurado de un sistema agéntico real usando el Promptware Kill Chain y el AATMF-R como herramientas de priorización
5. Articular el estado actual del campo: qué tiene solución efectiva, qué está en investigación activa, y qué permanece abierto

M5 es el módulo de cierre. El tono es diferente a M1–M4: más síntesis, más gobernanza, más orientado al "¿qué hacemos con todo esto?". El caso integrador es el corazón del módulo.

---

## Perfil de la audiencia — ajuste M5

La audiencia llega con cinco módulos de contexto. Es probable que:
- Estén pensando en sus propios sistemas y en cómo aplicar lo aprendido
- Quieran hablar de implementación concreta, no solo de principios
- Tengan curiosidad sobre los estándares para poder usar el lenguaje en sus organizaciones
- Estén listos para el debate sobre gobernanza y política

**Ajuste de tono para M5**: M5 es el módulo más "estratégico" del seminario. El bloque de CaMeL puede ir técnico (la audiencia lo va a apreciar), pero los bloques de estándares y el caso integrador deben ir a la aplicabilidad. La pregunta que organiza M5: "¿Qué podés hacer mañana, con el stack que tenés?"

**El riesgo de M5**: que se convierta en una lista de estándares aburrida. Evitar leer los acrónimos — contar por qué importa cada uno y qué permite hacer que antes no era posible.

---

## Script de clase — 4 horas (09:00–13:00)

### 09:00–09:10 · Apertura: el estado del arte (10 min)

**Qué decir**:
> "Cuatro módulos de ataques. Un módulo de defensa. Esa es la proporción real del campo — el conocimiento ofensivo supera al defensivo. Y hoy vamos a ver lo que existe, qué funciona, y dónde están los huecos que el campo no sabe cerrar todavía."

Mostrar la Slide 3 (las tres citas). No explicar — leer una de las tres y preguntar a la sala:
> "¿Con cuál de estas tres citas cerraron M4? ¿Por qué?"

*(La cita de Stuckey o de Aizen. El punto: estas son las citas de los CISOs y los investigadores más avanzados del campo. No es desaliento — es calibración.)*

---

### 09:10–10:00 · Bloque 1 — CaMeL (50 min) | Slides 3–6

**09:10–09:25 · Por qué los filtros no escalan (Slides 4, 15 min)**

La Slide 4 es conceptualmente la más importante del módulo. Empezar con la pregunta que la motiva:
> "¿Qué tienen en común el filtro de keywords, el clasificador de intent, y el LLM guardián? Todos intentan detectar el ataque. ¿Qué pasa si el atacante construye un payload que evade el detector?"

Dar tiempo para que respondan. El punto:
> "Es una carrera armamentista. El atacante construye un payload que evade el filtro. El defensor mejora el filtro. El atacante construye un payload que evade el nuevo filtro. Esto no tiene fin — porque el atacante y el defensor son fundamentalmente iguales: ambos usan LLMs, ambos generan texto."

La pregunta que CaMeL responde:
> "¿Hay una defensa que no dependa de detectar el ataque? Una defensa donde, aunque el payload llegue al contexto, no tenga ningún efecto?"

**09:25–09:50 · Arquitectura CaMeL (Slides 5–6, 25 min)**

Mostrar el diagrama de la Slide 5. Recorrerlo lentamente. El punto crítico es la separación:

> "El LLM Observador ve el email del cliente. Puede resumirlo, extraer información, clasificarlo. Pero no puede invocar tools. Aunque el email tenga un payload de IPI que diga 'ejecutá tal herramienta' — el LLM que lo procesa no tiene herramientas para ejecutar."

> "El LLM Privilegiado puede invocar herramientas. Pero nunca ve el contenido del email. Solo ve etiquetas abstractas — 'el email menciona una consulta sobre factura para el cliente CLI-001'. No puede ser manipulado por el contenido del email porque no lo ve."

La pregunta que genera discusión:
> "¿Qué aplicaciones de ARIA no podrían funcionar con la arquitectura CaMeL? ¿Qué tipo de tarea requeriría que el mismo LLM procese el contenido del email Y genere la acción en respuesta?"

Respuesta esperada: tareas donde la acción depende de un análisis semántico profundo del contenido — por ejemplo, si la respuesta al email requiere entender el tono emocional del cliente. CaMeL puede hacer eso con el LLM Observador procesando el tono, pero pasar solo una etiqueta ("tono_frustrado: true") al LLM Privilegiado puede perder matices.

Slide 6 (límites y aplicación a ARIA): el 33% restante es importante de mencionar:
> "CaMeL no es una solución completa. El 33% de ataques restantes en AgentDojo son los que explotan el LLM Privilegiado directamente — si el usuario puede inyectar instrucciones en el sistema prompt que ve ese LLM. Y la implementación de referencia no existe todavía en frameworks como LangChain o ADK. Es investigación activa."

**09:50–10:00 · La cita de Willison y lo que implica (10 min)**

La cita de Willison ("primera defensa creíble que no agrega más IA al problema") es el cierre conceptual del bloque:
> "Todos los filtros que mencionamos antes — el clasificador de intent, el LLM guardián — agregan más IA al problema. Usan IA para detectar ataques a la IA. CaMeL no hace eso. Hace separación de privilegios — el principio más viejo y robusto de seguridad de sistemas. El mismo principio del RBAC, del least privilege, del sandbox. Aplicado a LLMs."

---

### 10:00–10:45 · Bloque 2 — Toolkit completo (45 min) | Slides 7–9

**10:00–10:15 · Spotlighting (Slide 7, 15 min)**

El contraste con CaMeL es el punto pedagógico:
> "Spotlighting es una heurística de entrenamiento — el modelo fue entrenado para respetar los delimitadores. CaMeL es una restricción arquitectural — el LLM que procesa el contenido estructuralmente no puede invocar herramientas. ¿Cuál es más robusta ante un atacante sofisticado?"

La respuesta: CaMeL, porque Spotlighting puede ser evadido si el payload es suficientemente sofisticado para ignorar los delimitadores. Pero Spotlighting tiene la ventaja de ser implementable hoy con cualquier modelo sin reentrenamiento.

**10:15–10:30 · Instruction Hierarchy (Slide 8, 15 min)**

Conectar con el fundamento del M1:
> "En M1 establecimos que el system prompt no tiene enforcement arquitectónico — es RLHF behavior. Instruction Hierarchy es un intento de hacer ese enforcement más robusto mediante entrenamiento: el modelo aprende a dar menos peso a instrucciones en las posiciones de datos externos."

Los +63% de robustez: son mejoras reales pero no completas. El análogo a Spotlighting: más robusto que la línea base, pero no equivalente a la separación arquitectural de CaMeL.

**10:30–10:45 · Tabla síntesis (Slide 9, 15 min)**

La tabla es densa. No leerla — elegir tres filas para debatir:
> "Si su organización puede implementar solo tres defensas esta semana — con el stack existente, sin reentrenamiento, sin cambios arquitecturales mayores — ¿cuáles eligirían? ¿Por qué?"

Respuesta esperada: Least Agency + RAG trust level separation + Spotlighting/delimitadores — las tres de mayor impacto implementables hoy con prompt engineering y diseño de sistema.

---

### 10:45–11:15 · Bloque 3 — Estándares (30 min) | Slides 10–12

Este bloque va a tener velocidad. La audiencia no necesita memorizar los estándares — necesita saber para qué sirve cada uno.

**10:45–10:55 · OWASP Top 10 Agéntico (Slide 10, 10 min)**

Hacer el ejercicio inverso — proyectar la tabla y preguntar a la sala:
> "EchoLeak de M2 — ¿qué item del Top 10 Agéntico?"

Respuesta: ASI01 (Agent Goal Hijack). Continuar:
> "Tool Poisoning de M3?"  → ASI04 (Agentic Supply Chain)
> "SpAIware de M4?" → ASI06 (Memory & Context Poisoning)
> "Morris II de M3?" → ASI08 (Cascading Failures) y ASI07 (Insecure Inter-Agent Communication)

Esto concretiza el valor del estándar: permite clasificar cualquier incidente en un lenguaje reconocido.

**10:55–11:05 · MITRE ATLAS y NIST IR 8596 (Slide 11, 10 min)**

Para MITRE ATLAS, dar los identificadores reales de las técnicas más relevantes:
> "AML.T0051 es Prompt Injection. AML.T0054 es Jailbreak. AML.T0057 es Plugin Compromise. Si en un informe de seguridad usás esos identificadores, cualquier SOC o CERT que lo lea va a entender exactamente de qué hablás."

Para NIST IR 8596, conectar con el contexto institucional:
> "Si están en una organización regulada — banco, hospital, gobierno — NIST IR 8596 es la forma de hablar de estos riesgos con compliance y con legal. Traduce los ataques técnicos de este seminario al lenguaje de riesgo y gobernanza que esas audiencias entienden."

**11:05–11:15 · AATMF (Slide 12, 10 min)**

El valor práctico del AATMF-R es central para el caso integrador:
> "El AATMF-R es el único de los tres estándares que da un número. OWASP da un ranking, ATLAS da un nombre, AATMF-R da un score cuantitativo. Eso permite responder preguntas de gestión: ¿cuánto reduce el riesgo implementar esta defensa? ¿Cuál de dos arquitecturas tiene menor riesgo residual?"

---

### 11:15–11:30 · Pausa (15 min)

---

### 11:30–12:30 · Bloque 4 — Caso integrador (60 min) | Slides 13–14

**El ejercicio más largo del seminario**. 35 minutos de trabajo, 15 de puesta en común.

**11:30–11:35 · Setup del ejercicio (5 min)**

Distribuir M5-ejercicio-ARIA.md. Contextualizar:
> "Este es el ejercicio final del seminario. Usan todo — los cinco módulos. La Pregunta 1 es threat modeling. La Pregunta 2 es el ataque más sofisticado que aprendieron a construir. La Pregunta 3 es la arquitectura defensiva. Tienen 35 minutos — van a tener que priorizar."

Dar una recomendación explícita:
> "Si el tiempo presiona: Pregunta 3 es la más importante. El caso integrador que importa es que puedan diseñar una arquitectura que cubra los vectores de múltiples módulos. La Pregunta 1 es el setup que la hace posible."

**11:35–12:10 · Trabajo en grupos (35 min)**

Circular activamente. Los puntos de atención:

- En la **Pregunta 1**: verificar que el threat model incluye técnicas de al menos tres módulos distintos. Si un grupo solo tiene técnicas de un módulo, preguntarles sobre los otros.

- En la **Pregunta 2**: el ataque encadenado de mayor impacto es el que usa tres etapas del Kill Chain en una sola cadena (Initial Access via IPI → Privilege Escalation via tool misuse → Persistence via LTM). Si algún grupo lo construye, pedirles que lo presenten.

- En la **Pregunta 3**: verificar que la arquitectura defensiva es coherente — que las defensas propuestas cubren los vectores del threat model, no defensas genéricas. "Usar CaMeL" sin especificar cómo aplica a ARIA v5 no es suficiente.

**12:10–12:30 · Puesta en común (20 min)**

Tiempo extendido para la puesta en común del caso integrador — es el cierre del seminario.

- Pedir a un grupo que presente el ataque encadenado de la Pregunta 2 (el más sofisticado) — 5 minutos
- Pedir a otro grupo que presente la arquitectura defensiva de la Pregunta 3 — 5 minutos
- 10 minutos de discusión abierta: ¿qué quedó sin cubrir? ¿Qué defensa hubiera requerido cambios de modelo o de plataforma, no solo de configuración?

---

### 12:30–13:00 · Cierre del seminario (30 min) | Slides 15–20

**12:30–12:45 · El mapa completo y las fronteras abiertas (Slides 15–16, 15 min)**

Slide 15: la Kill Chain completa — recorrerla en silencio con la sala. Dejar que la magnitud del mapa se instale.

Slide 16 (fronteras abiertas): este es el momento más honesto del seminario. No endulzarlo:
> "Sleeper Agents: no hay defensa. Trojan Tools a escala industrial: no hay defensa automatizada. PIP a largo plazo: difícil de detectar. In-weights attacks: sin reentrenamiento, sin solución. El campo avanza rápido pero hay problemas fundamentales que siguen abiertos."

**12:45–13:00 · Cierre (Slides 17–20, 15 min)**

Slides 17–18: rápidas — señalar las áreas de investigación activa sin leer en detalle.

Slide 19 (trabajo final): las fechas son lo más importante. Dar el link o la ubicación del documento M5-trabajo-final.md.

Slide 20: la cita final de Rehberger. Tomarse el tiempo para que resuene:
> "The LLM is not a trustworthy actor in your threat model. All security controls must be implemented downstream of LLM output."

Y la conclusión del instructor:
> "Eso resume los cinco módulos. El LLM no es trustworthy — no por negligencia ni por mal diseño, sino por cómo funciona la arquitectura. Los controles van en el runtime, en la arquitectura del sistema, en los estándares de gobernanza. Eso es lo que construimos acá."

---

## Preguntas frecuentes anticipadas

**"¿CaMeL ya está implementado en algún producto?"**
> El paper de Google DeepMind (abril 2025) describe la arquitectura. No hay una implementación de referencia publicada en frameworks mainstream (LangChain, ADK, LangGraph) a la fecha del seminario. Algunos equipos de seguridad avanzados lo están implementando manualmente. Es previsible que para 2027 haya implementaciones de referencia en los frameworks principales.

**"¿El OWASP Top 10 Agéntico es un estándar oficial?"**
> OWASP es una fundación sin fines de lucro, no un organismo de estandarización oficial como ISO o NIST. Pero sus publicaciones son ampliamente reconocidas como referencia de facto en la industria. El Top 10 Agéntico tiene el mismo status que el Top 10 para LLMs — no es obligatorio por ley, pero muchas organizaciones lo usan como checklist de auditoría y los contratos de seguridad suelen referenciarlo.

**"¿El AATMF-R tiene benchmarks de referencia — cuánto es un score 'bueno' vs. 'malo'?"**
> No hay benchmarks públicos estandarizados todavía. El AATMF es relativamente reciente (2026). El valor práctico es comparativo: un sistema con AATMF-R total de 200 tiene mayor riesgo que uno con 80. O: implementar una defensa que reduce el score de 200 a 120 tiene ROI mayor que una que lo reduce de 200 a 180. No hay un umbral absoluto de "seguro".

**"¿Spotlighting es suficiente para producción, o siempre hay que ir a CaMeL?"**
> Depende del contexto de riesgo. Para un chatbot de bajo impacto con tools de solo lectura, Spotlighting más Least Agency puede ser suficiente. Para un sistema como ARIA (400 empresas, herramientas de escritura en CRM, envío de emails) el riesgo justifica ir más lejos. La decisión correcta se hace con el AATMF-R: calcular el riesgo residual con Spotlighting solamente y compararlo con el riesgo aceptable para el sistema.

---

## Materiales necesarios

- Presentación (adaptar slides desde M5-slides.md)
- Handout del ejercicio (M5-ejercicio-ARIA.md) — imprimir 1 copia por grupo
- Documento del trabajo final (trabajo-final.md) — proyectar o distribuir digitalmente
- Setup Colab (setup-colab.md) — asegurarse de que todos lo tienen para el trabajo final si incluye labs
