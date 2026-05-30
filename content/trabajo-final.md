# Trabajo Final — Seminario: Seguridad en Agentes de IA, Amenazas, Riesgos y Gobernanza

> **Institución**: Universidad Nacional de La Plata, Facultad de Informática, Secretaría de Postgrado  
> **Entrega**: 30 días a partir del Módulo 5 (hasta el 14 de septiembre de 2026)  
> **Modalidad**: Individual o grupos de hasta 3 personas  
> **Formato de entrega**: Informe técnico (PDF) + presentación de 15 minutos (fecha a coordinar)

---

## Objetivo del trabajo

El trabajo final integra los cinco módulos del seminario en un análisis completo de seguridad de un sistema agéntico real o de complejidad equivalente. El objetivo es demostrar la capacidad de:

1. Identificar la superficie de ataque de un sistema agéntico usando los marcos conceptuales del seminario
2. Modelar amenazas concretas con el Promptware Kill Chain y la Trifecta Letal
3. Priorizar riesgos cuantitativamente usando el AATMF-R
4. Proponer una arquitectura defensiva técnicamente fundamentada con análisis de trade-offs

---

## Elección del sistema

### Opción A — Sistema real

Elegir un sistema agéntico en producción, en desarrollo activo, o documentado públicamente. Ejemplos posibles:
- Un agente de coding (GitHub Copilot Workspace, Cursor con agentes, Claude Code)
- Un asistente empresarial con herramientas (Microsoft 365 Copilot, Salesforce Einstein Agents)
- Un agente de automatización (n8n con IA, Zapier AI, Make con agentes)
- Un sistema interno propio o de la organización donde trabajás (puede ser anonimizado)
- Un sistema de código abierto (AutoGPT, MetaGPT, BabyAGI, AgentGPT)

**Requisito mínimo para un sistema real**: debe tener al menos un LLM con acceso a herramientas (tool use), al menos una fuente de datos externa (web, base de datos, API, email), y capacidad de ejecutar acciones con efectos en el mundo (enviar mensajes, modificar datos, generar código ejecutado).

### Opción B — Sistema ficticio

Diseñar un sistema agéntico ficticio de complejidad equivalente a ARIA v5. El sistema debe:
- Tener un caso de uso real y concreto (no abstracto)
- Incluir al menos 5 herramientas con especificaciones técnicas
- Tener al menos un componente de memoria (RAG, LTM, o ambos)
- Operar en un dominio con consecuencias reales de alto impacto (finanzas, salud, legal, infraestructura crítica, o similar)
- Especificar el stack técnico: modelo, framework, herramientas, infraestructura

---

## Estructura del informe

El informe tiene cinco secciones obligatorias. La extensión total es 15–20 páginas (sin contar anexos).

### Sección 1 — Descripción del sistema (2–3 páginas)

**Contenido obligatorio**:
- Descripción del caso de uso y el contexto organizacional
- Stack técnico: modelo(s), framework de orquestación, herramientas disponibles, fuentes de datos
- Diagrama de arquitectura (puede ser ASCII art o imagen)
- Flujo operativo típico: cómo funciona el sistema en un caso normal de uso
- Identificación de los 5 componentes de un agente (modelo base, system prompt, herramientas, memoria, runtime) en el sistema específico

**Criterio de evaluación**: precisión técnica de la descripción. La audiencia lectora es técnica — se espera especificidad (nombres reales de herramientas, schemas aproximados, datos reales de escala).

### Sección 2 — Threat Model con el Promptware Kill Chain (4–5 páginas)

**Contenido obligatorio**:

Para cada etapa del Promptware Kill Chain aplicable al sistema:

| Etapa | Técnicas aplicables | Vector(es) concretos | Actores de amenaza | Prerequisitos del atacante |
|---|---|---|---|---|
| Initial Access | | | | |
| Privilege Escalation | | | | |
| Persistence | | | | |
| Lateral Movement | | | | |
| Actions on Objective | | | | |

**Para las etapas que apliquen**, desarrollar en detalle:
- El payload concreto (texto del email, nombre del documento, descripción del tool, etc.)
- El flujo completo del ataque (modelo del estilo de los ejercicios de M2–M4)
- Qué logs o alertas generaría (o no generaría) el ataque
- El impacto estimado (datos afectados, usuarios afectados, reversibilidad)

**Criterio de evaluación**: que los ataques sean técnicamente coherentes con los mecanismos del seminario. Un ataque que "el LLM haría porque es vulnerable" sin especificar el mecanismo no es suficiente. Se espera: vector → contenido del payload → entrada al contexto → acción resultante → evidencia forense.

### Sección 3 — Priorización con AATMF-R (2–3 páginas)

**Contenido obligatorio**:

Para las **5 técnicas de mayor riesgo** identificadas en la Sección 2:

| Técnica | Likelihood (1–5) | Impact (1–5) | Detectability (1–5, inverso) | Recoverability (1–5, inverso) | AATMF-R |
|---|---|---|---|---|---|

**Donde**:
- **Likelihood**: probabilidad de que un atacante con recursos estándar pueda ejecutar el ataque (1=muy baja, 5=trivialmente ejecutable)
- **Impact**: magnitud del daño si el ataque tiene éxito (1=mínimo, 5=catastrófico e irreversible)
- **Detectability** (inverso): qué tan difícil es detectar el ataque en los logs/sistemas existentes (1=fácil de detectar, 5=indetectable)
- **Recoverability** (inverso): qué tan difícil es recuperarse del ataque (1=trivialmente reversible, 5=daño permanente)
- **AATMF-R** = Likelihood × Impact × Detectability × Recoverability

**Justificar** cada score con argumentos técnicos concretos referenciados a los mecanismos del seminario.

**Criterio de evaluación**: consistencia de los scores con los mecanismos técnicos descritos en la Sección 2. Un score de Detectability=5 para un ataque que genera emails visibles en el EMAIL_LOG no es consistente.

### Sección 4 — Arquitectura defensiva (4–5 páginas)

**Contenido obligatorio**:

Para cada una de las 5 técnicas de mayor AATMF-R:

1. **La defensa de mayor ROI**: cuál es la única modificación que más reduce el riesgo de esa técnica
2. **Análisis de trade-off**: qué condición de la Trifecta elimina o reduce, cuánto reduce el AATMF-R estimado, y qué costo tiene para la utilidad del sistema
3. **Implementabilidad**: en el stack técnico actual del sistema, ¿es implementable hoy? ¿Requiere cambios de framework, reentrenamiento, o solo configuración?

**Diagrama de arquitectura defensiva** (obligatorio): el sistema con las defensas propuestas, mostrando qué cambió respecto al diagrama de la Sección 1.

**Análisis de cobertura residual**: después de implementar las defensas propuestas, ¿qué técnicas del Kill Chain siguen siendo alcanzables? ¿Con qué probabilidad reducida?

**Criterio de evaluación**: que las defensas sean específicas para las técnicas que mitigan. "Mejorar la seguridad en general" no es una defensa — "implementar Spotlighting en el procesamiento de emails con el delimitador [EMAIL_START]/[EMAIL_END] y sistema prompt reforzado de desconfianza" sí lo es.

### Sección 5 — Mapping a estándares (1–2 páginas)

**Contenido obligatorio**:

Mapear las 5 técnicas de mayor riesgo a:
- **OWASP Top 10 Agéntico**: cuáles de los 10 ASI items aplican al sistema analizado
- **MITRE ATLAS**: al menos 3 técnicas del análisis mapeadas a identificadores AML.T (usar la taxonomía de octubre 2025 o posterior)
- **NIST AI RMF**: qué función del RMF (GOVERN/MAP/MEASURE/MANAGE) no está adecuadamente cubierta en el sistema actual

**Criterio de evaluación**: que el mapping sea correcto — un ataque de RAG Poisoning no es ASI02 (Tool Misuse), es ASI06 (Memory & Context Poisoning).

---

## Criterios de aprobación

### Aprobación: 60/100 puntos mínimo

| Sección | Puntaje máximo | Criterios |
|---|---|---|
| **Sección 1** — Descripción del sistema | 15 pts | Precisión técnica, completitud de los 5 componentes, claridad del diagrama |
| **Sección 2** — Threat Model | 30 pts | Coherencia de los ataques con los mecanismos del seminario, especificidad de los payloads, análisis forense de evidencias |
| **Sección 3** — AATMF-R | 15 pts | Justificación de los scores, consistencia con la Sección 2 |
| **Sección 4** — Arquitectura defensiva | 30 pts | Especificidad de las defensas, análisis de trade-offs, implementabilidad, cobertura residual |
| **Sección 5** — Mapping a estándares | 10 pts | Corrección del mapping, argumentación |

### Criterios diferenciadores (distinguen aprobado de destacado)

**Notable (80–89 pts)**:
- Los ataques de la Sección 2 incluyen al menos un ataque encadenado que usa técnicas de módulos distintos
- Los scores AATMF-R están respaldados por datos o benchmarks citados del seminario (Greshake, PoisonedRAG, MCP Safety Audit, etc.)
- La arquitectura defensiva incluye análisis de cobertura residual con AATMF-R estimado post-defensa

**Distinguido (90–100 pts)**:
- El análisis identifica una técnica o vector que no fue cubierto explícitamente en el seminario pero es relevante para el sistema específico, con justificación técnica
- La arquitectura defensiva incluye un diagrama implementable con código o pseudocódigo para al menos una defensa
- El informe podría ser usado como documento de referencia por el equipo de seguridad de la organización del sistema analizado

### Criterios de rechazo automático

- Atacar o defender un sistema sin especificar mecanismos técnicos (solo describir lo que el LLM "haría")
- Clasificar técnicas incorrectamente en los estándares (errores de OWASP/ATLAS)
- AATMF-R scores sin justificación o internamente inconsistentes
- Menos de 15 páginas sin justificación
- Ausencia de cualquiera de las 5 secciones obligatorias

---

## Formato y entrega

- **Formato**: PDF, tipografía legible, márgenes estándar
- **Código y payloads**: en bloques de código con syntaxis highlighting
- **Diagramas**: ASCII art o imágenes incluidas en el PDF
- **Citas**: cualquier formato consistente (IEEE, APA, o informal con URL+fecha)
- **Anonimización**: si el sistema es real y tiene datos sensibles, anonimizar nombres de clientes/usuarios pero mantener la especificidad técnica
- **Entrega**: por el campus del curso o al email del instructor antes de las 23:59 del 14 de septiembre de 2026

---

## Presentación oral (opcional pero recomendada)

Una sesión de presentaciones orales será organizada en la semana del 22 de septiembre. La presentación es opcional — solo el informe escrito es obligatorio para la aprobación. Quienes presenten recibirán feedback adicional del instructor y podrán mejorar su nota con la claridad de la exposición oral.

**Formato de la presentación**: 15 minutos de exposición + 5 minutos de preguntas. Slide deck opcional — puede ser una demostración en vivo de un lab o una discusión técnica libre.

---

## Preguntas frecuentes

**¿Puedo analizar un sistema de IA que no sea un "agente" en el sentido estricto del seminario?**  
El sistema debe tener capacidad de acción (tool use) para que el análisis tenga profundidad. Un chatbot puro sin herramientas no tiene Confused Deputy ni Tool Shadowing — el análisis queda vacío. Si tu sistema no tiene herramientas, hablar con el instructor antes de empezar.

**¿Puedo analizar el mismo sistema que otro grupo?**  
Sí, pero los análisis tienen que ser independientes. Dos grupos pueden analizar el mismo sistema y llegar a conclusiones diferentes — eso es válido si está justificado.

**¿Puedo incluir un lab implementado en Colab como parte del trabajo?**  
Sí, como anexo. Un lab que demuestra un ataque descripto en la Sección 2 suma evidencia pero no reemplaza el análisis escrito.

**¿El sistema puede ser el que usamos en el trabajo o tesis?**  
Idealmente sí — es la mejor forma de que el trabajo tenga valor más allá del seminario.
