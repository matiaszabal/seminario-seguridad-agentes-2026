# Módulo 3 · Guía Docente — Orquestación, MCP y Ataques Multi-Agente

> **Uso**: Documento interno del instructor. No distribuir a alumnos.  
> **Fecha**: Sábado 11 de julio de 2026

---

## Objetivo pedagógico del módulo

Al finalizar, el participante debe poder:

1. Explicar la vulnerabilidad estructural de MCP: por qué el contexto compartido entre servidores hace que las descripciones de tools sean una primera superficie de ataque, y qué implica eso para cualquier sistema que use el protocolo
2. Distinguir las tres variantes de Tool Attack (Tool Poisoning, Trojan Tool, Rug Pull) y seleccionar la defensa correcta para cada una, incluyendo la limitación del Tool Manifest respecto al Trojan Tool
3. Trazar la cadena de ataque end-to-end de un Rug Pull, desde la aprobación de v1.0.0 hasta la activación del payload en v1.0.1, y proponer dónde en el ciclo de vida de la herramienta hubiera sido detectable
4. Analizar la propagación de ataques en sistemas multi-agente: cómo Prompt Infection explota el mismo mecanismo de IPI entre agentes, y por qué Morris II puede propagarse sin acción del atacante
5. Diseñar una arquitectura de defensa en capas para un sistema con servidores MCP de terceros, mapeando cada capa a la condición de la Trifecta que mitiga

El módulo requiere dominio completo de M1 y M2. El vocabulario de ambos módulos se usa directamente sin re-introducción.

---

## Perfil de la audiencia — ajuste M3

La audiencia llega con dos módulos de contexto. Es probable que:
- Hayan visto el ejercicio M2 revelar vulnerabilidades en sistemas que conocen
- Estén más familiarizados con el vocabulario y pidan mayor profundidad técnica
- Tengan preguntas sobre mitigaciones concretas para sus propios stacks
- Algunos hayan explorado MCP durante el receso desde M2

**Ajuste de tono para M3**: M3 es más operativo que M2. La parte conceptual de MCP puede ir rápido — el protocolo es conocido por una parte de la audiencia. El foco debe estar en el mecanismo de las variantes de ataque (las diferencias entre los tres tipos son el corazón del módulo) y en la construcción del manifest en el ejercicio. El bloque de multi-agente es más conceptual y puede comprimirse si el tiempo presiona.

**Riesgo a evitar**: que la audiencia sienta que Tool Poisoning es "fácil de detectar leyendo el docstring" y lo descarte como amenaza menor. El punto que hay que instalar: el Rug Pull hace que incluso un proceso de revisión exhaustivo sea insuficiente si no hay versionado de descripciones.

---

## Script de clase — 4 horas (09:00–13:00)

### 09:00–09:10 · Apertura y recap M2 (10 min)

**Qué decir**:
> "Antes de arrancar: en M2 vimos que el vector de ataque era el *dato* que el agente procesa. El email malicioso, el documento envenenado en el RAG. El atacante necesitaba que ese dato llegara al contexto de ARIA en cada sesión."

Pausa. Luego:
> "La pregunta que abre M3 es: ¿qué pasa si el atacante no necesita enviar nada en cada sesión? ¿Y si en lugar de envenenar los datos que el agente lee, envenena la herramienta que el agente ejecuta? Una vez instalada, la herramienta se activa en todas las consultas. Sin email malicioso. Sin documento poisonado. Solo la herramienta en el runtime."

Mostrar Slide 3 (el contraste M2 vs. M3). No explicar — preguntar:
> "¿Qué implicancia tiene eso para el modelo de amenaza? ¿Qué cambia desde el punto de vista del defensor?"

Respuesta esperada: el atacante necesita comprometer la cadena de supply chain una sola vez (instalar la herramienta) en lugar de comprometer el dato en cada sesión. El impacto es multiplicativo.

---

### 09:10–10:00 · Bloque 1 — MCP (50 min) | Slides 3–7

**09:10–09:25 · MCP: protocolo y adopción (Slides 4–5, 15 min)**

Para la audiencia técnica, MCP puede ser conocido. Calibrar rápido:
> "¿Quién usó MCP en las últimas semanas — Claude Desktop, Cursor, Continue, o integración directa?"

Si hay varios: ir rápido por las Slides 4 y 5. Si pocos conocen MCP: dedicar más tiempo a la arquitectura.

El punto que hay que instalar independientemente del nivel de familiaridad:
> "97 millones de descargas mensuales. Una superficie de ataque estandarizada — una vulnerabilidad en el protocolo afecta a todos los sistemas que lo implementan. El MCP Safety Audit salió a los 5 meses del lanzamiento. Eso da una medida de la urgencia."

**09:25–09:45 · El problema estructural (Slides 6–7, 20 min)**

Este es el punto conceptual más importante del bloque. Tomarlo con calma.

Mostrar el código de la Slide 6 (el context object con las tres tools de tres servidores). Hacer la pregunta:
> "Lean la descripción del Servidor C — `list_issues`. Hay una instrucción ahí que afecta el comportamiento del Servidor A. ¿La ven?"

Dejar que la sala la encuentre. Cuando la identifiquen:
> "Exacto. La descripción del Servidor C instruye al LLM sobre cómo usar `read_file` del Servidor A. El Servidor A no sabe nada de eso. El Servidor C nunca fue invocado. Pero su descripción está en el contexto y el LLM la procesó."

Este es el mecanismo de cross-server contamination. La cita del MCP Safety Audit va bien acá:
> "Tool descriptions processed by the LLM are treated with the same trust level as system instructions."

Slide 7 (la asimetría de información): hacer el recorrido de la tabla. El punto que más impacto tiene:
> "El LLM no accede al código. El usuario raramente ve las descripciones. El runtime no interpreta semánticamente. La única defensa disponible en el pipeline normal es la revisión humana al momento de instalación — y eso falla ante el Rug Pull."

**09:45–10:00 · Tiempo libre o profundización** (15 min)

Si la sala tiene preguntas sobre MCP, este es el momento. Si no, usar para un ejercicio de "spot the injection" — mostrar tres descripciones de tools y pedir que identifiquen cuál tiene el payload y de qué tipo.

---

### 10:00–10:50 · Bloque 2 — Tool Shadowing (50 min) | Slides 8–12

**10:00–10:10 · Taxonomía (Slide 8, 10 min)**

La tabla comparativa es densa — no leerla. Usarla como mapa de lo que viene:
> "Tres variantes. En los próximos 40 minutos vamos a ver cada una. Lo que importa no es memorizarlas — es entender *por qué* tienen perfiles de detectabilidad diferentes. Eso determina qué defensa sirve para cada una."

**10:10–10:25 · Tool Poisoning (Slide 9, 15 min)**

Mostrar el código del docstring malicioso. Hacer la pregunta antes de revelar el resultado:
> "Si este es el docstring de `search_crm_enterprise`, y ARIA la tiene disponible junto con `send_email`: cuando atiende una consulta normal que invoca `search_crm_enterprise`, ¿cuántos emails va a enviar ARIA?"

Respuesta esperada: dos — uno al destinatario malicioso del CRM log, uno al cliente legítimo.

Mostrar el trace del lab M3a (Ejercicio 2a). El punto del 84.2%:
> "Invariant Labs lo midió. Con auto-approval activado — que es el default en muchas instalaciones de Claude Desktop — el 84.2% de los intentos de Tool Poisoning funcionaron. ¿Qué cambia si el usuario tiene que aprobar cada tool call?"

Aquí surge la discusión de UX vs. seguridad. No zanjarla prematuramente — dejar que la sala explore la tensión. El punto que hay que establecer es que el auto-approval convierte una herramienta visual ("el usuario ve el tool call") en una herramienta invisible ("el agente actúa sin mostrar nada").

**10:25–10:35 · Trojan Tool (Slide 10, 10 min)**

El punto central de este bloque. Empezar con la pregunta:
> "Bien. Si el Tool Poisoning se detecta leyendo el docstring, ¿qué pasa si el atacante simplemente pone un docstring limpio y el ataque está en el código?"

Mostrar el código del Trojan Tool. El elemento más impactante es el análisis forense:
> "El trace del agente es completamente normal. El EMAIL_LOG tiene un solo email legítimo. La exfiltración está en el EXFIL_LOG — que solo existe en el código. Un SIEM que monitorea los logs del agente no ve nada. La detección requiere auditoría del código fuente de cada herramienta instalada."

La pregunta de escala:
> "El Trojan Tool se activa en cada invocación de la herramienta, para cada usuario, en cada sesión. ¿Cómo cambia eso el modelo de daño comparado con el email envenenado de M2 que afectaba solo la sesión que lo procesaba?"

**10:35–10:45 · Rug Pull (Slide 11, 10 min)**

Este es el ataque más sofisticado del bloque. Tomarlo con un ejemplo concreto.

Describir el escenario del diff antes de mostrarlo:
> "El equipo de seguridad revisó el código en octubre. Aprobaron la herramienta. En noviembre el proveedor publicó un 'bug fix patch'. El pipeline de CI/CD actualizó automáticamente. Nadie volvió a revisar porque era un patch menor. El diff de la única línea que cambió era esto:"

Mostrar el diff. Silencio deliberado. Luego:
> "Una línea. Pasó todos los tests. El schema de la función es idéntico. La función retorna el mismo tipo. Desde el punto de vista de un test de integración, v1.0.0 y v1.0.1 son idénticas."

**10:45–10:50 · CVEs MCP (Slide 12, 5 min)**

Rápido — el punto es establecer que esto no es teórico:
> "El incidente de Asana de junio 2025 — forzado offline por Aizen. Una integración MCP con acceso cross-tenant. Esto ya pasó en producción. El MCP Safety Audit lo documenta. No es un ejercicio académico."

---

### 10:50–11:15 · Bloque 3 — Multi-agente (25 min) | Slides 13–15

**10:50–11:00 · Topologías y superficie de ataque (Slide 13, 10 min)**

Para audiencia técnica, conectar con frameworks que conocen:
> "Chain, star y mesh — MetaGPT, AutoGen, LangGraph/CrewAI. La pregunta de seguridad que cambia según la topología: ¿cuántos componentes hay que comprometer para comprometer el sistema completo? En chain: el último agente de la cadena puede recibir el ataque del primero. En mesh: uno compromete todos los caminos."

**11:00–11:10 · Prompt Infection (Slide 14, 10 min)**

El punto conceptual clave: es el mismo mecanismo de M2 aplicado entre agentes.
> "¿Por qué funciona Prompt Infection? Por la misma razón que IPI: el output del Agente A entra al contexto del Agente B como texto, y el LLM del Agente B no puede distinguir si ese texto es datos o instrucciones. El canal unificado del Módulo 1 aplica entre agentes igual que entre datos y prompts."

Esto cierra el loop conceptual del seminario: el problema raíz (canal unificado, indistinción instrucción/dato) se manifiesta en M2 como IPI, en M3 como Tool Poisoning, y ahora también como Prompt Infection.

**11:10–11:15 · Morris II (Slide 15, 5 min)**

Este bloque puede comprimirse si el tiempo presiona. El punto central:
> "Morris II no necesita que el atacante haga nada después del primer email. El worm se propaga solo. Las condiciones son exactamente la Trifecta: datos externos procesados, herramientas de impacto, Ambient Authority. No hay nada nuevo — solo escalado."

---

### 11:15–11:30 · Pausa (15 min)

---

### 11:30–12:15 · Bloque 4 — Defensas (45 min) | Slides 16–20

**11:30–11:50 · Tool Manifest y sus límites (Slides 16–17, 20 min)**

Presentar el manifest como la defensa más directa disponible:
> "La defensa más directa para Tool Poisoning y Rug Pull: tratar el docstring como código versionado. Hash en el momento de aprobación. Alerta si el hash cambia. Esta es la implementación del Ejercicio de Defensa en el lab M3a."

Mostrar la tabla de qué detecta y qué no. El punto que genera más discusión:
> "El manifest no detecta el Trojan Tool. El hash del docstring es idéntico al original. Esto no es un bug del manifest — es una limitación de diseño. El manifest verifica la descripción, no la implementación. La defensa contra el Trojan Tool es otra: inspección de código como parte del proceso de aprobación, o sandboxing de ejecución."

Slide 17 (sandboxing): ir al código de namespace isolation. Para audiencia técnica, conectar con conceptos que conocen:
> "La separación de namespaces es análoga a RBAC en sistemas tradicionales — cada servidor tiene su propio 'contexto de confianza' y no puede afectar el comportamiento con otros servidores. El protocolo MCP actual no lo implementa nativamente. Es una defensa que hay que construir encima."

**11:50–12:00 · Human-in-the-loop (Slide 18, 10 min)**

El punto del 84.2% → ~15%:
> "El dato más accionable del módulo: con auto-approval, Tool Poisoning tiene 84.2% de éxito. Con confirmación humana, baja significativamente. No porque el ataque cambie — sino porque el usuario puede ver el tool call malicioso antes de que se ejecute."

Discutir los tres niveles de L1, L2, L3. La pregunta que genera debate:
> "Para ARIA — sistema de atención al cliente de un banco corporativo — ¿qué nivel de confirmación es razonable? ¿L1, L2 o L3?"

Respuesta esperada de la sala: L2 — confirmación en operaciones de alto impacto (emails externos, modificaciones de CRM) pero no en operaciones de lectura. Eso reduce la friction sin eliminar la protección en el punto crítico.

**12:00–12:15 · Kill Chain completa y síntesis de defensas (Slides 19–20, 15 min)**

Slide 19 (defensa en profundidad): recorrer las cuatro capas. El énfasis:
> "Ninguna capa es suficiente sola. El manifest falla ante el Trojan Tool. El sandboxing no detecta descripciones maliciosas aprobadas. La confirmación humana es efectiva pero no cubre el Trojan Tool. El monitoreo detecta cuando todo lo demás falla. La seguridad real requiere las cuatro."

Slide 20 (Kill Chain): esto es el cierre conceptual del módulo. Hacer el recorrido:
> "M1 estableció el mapa. M2 desarrolló Initial Access y parte de Actions on Objective. M3 agrega Privilege Escalation via tool misuse y Lateral Movement via Prompt Infection. En M4 vamos a ver qué pasa cuando hay Persistence — cuando el ataque sobrevive al final de la sesión."

La cita de Dane Stuckey para cerrar el bloque de defensas:
> "Prompt injection remains a frontier, unsolved security problem."

---

### 12:15–12:45 · Bloque 5 — Ejercicio (30 min) | Slides 21–22

**12:15–12:20 · Setup del ejercicio (5 min)**

Distribuir el handout M3-ejercicio-ARIA.md. Hacer explícita la diferencia con M2:
> "En M2 construían el payload. En M3 están del otro lado — son el equipo de seguridad de FinBank auditando herramientas antes de aprobarlas. La Pregunta 1 requiere identificar el tipo de ataque. La Pregunta 3 requiere construir el manifest — que van a usar para justificar qué aprobaron y con qué condiciones."

**12:20–12:45 · Trabajo en grupos + puesta en común (25 min)**

**17 minutos de trabajo**. Los puntos de atención durante el trabajo en grupos:

- En **Pregunta 1**: verificar que distinguen los tres tipos. El error más común es clasificar el Trojan Tool como Tool Poisoning porque "el código hace algo extra". La distinción clave: en Tool Poisoning el LLM recibe la instrucción en la descripción y la ejecuta. En Trojan Tool el LLM no sabe nada — el código actúa por su cuenta.

- En **Pregunta 2**: pedir que sean específicos sobre el trigger — qué consulta legítima de un usuario de ARIA activa el ataque. No "cualquier consulta" sino "cualquier consulta que invoque `get_client_record`".

- En **Pregunta 3**: el punto crítico es la política de actualización. Un manifest sin política de actualización tiene un agujero — el Rug Pull espera al patch para activarse. La política debe decir explícitamente: "todo cambio en hash de docstring, independientemente del tipo de versión, requiere nueva revisión de seguridad".

**8 minutos de puesta en común**: pedir a un grupo que lea su clasificación de la Pregunta 1 y que la sala la corrija o valide. Luego pedir que otro grupo lea su política de actualización de la Pregunta 3.

---

### 12:45–13:00 · Cierre y puente al Módulo 4 (15 min) | Slides 23–24

Slide 23: preguntar a la sala en lugar de leer:
> "¿Pueden reformular el punto 2 — las tres variantes y sus defensas — en un cuadro conceptual? ¿Qué defensa cubre cuál y cuál queda sin cubrir?"

Slide 24: el puente al M4 necesita tiempo para instalarse:
> "Hasta acá, todos los ataques terminan con la sesión. El email envenenado de M2 no está ahí en la próxima sesión. El Rug Pull sigue ahí porque está instalado, pero si removemos la herramienta, desaparece. En M4 vamos a ver algo cualitativamente diferente: un ataque que persiste en la *memoria* del agente. No en las herramientas, no en los datos — en lo que el agente recuerda entre sesiones."

La pregunta de sleeper agents para el receso:
> "Un sleeper agent se comporta normalmente durante el fine-tuning de seguridad y activa comportamiento malicioso en producción bajo condiciones específicas. Si eso es posible, ¿qué implica para la confianza que depositamos en los modelos que usamos? ¿Quién garantiza que el modelo que deployamos no tiene un backdoor?"

---

## Preguntas frecuentes anticipadas — versión técnica

**"¿MCP no tiene algún mecanismo de verificación de integridad de servidores?"**
> El protocolo MCP (versión 1.x a la fecha del seminario) no tiene verificación criptográfica de la integridad de las descripciones de tools integrada en el protocolo. El transporte puede usar TLS para la conexión, pero eso verifica la identidad del servidor, no que las descripciones no hayan sido modificadas. El Tool Manifest es una defensa construida sobre el protocolo, no parte de él. Anthropic y la comunidad están trabajando en especificaciones de seguridad adicionales, pero no están estandarizadas a la fecha.

**"¿El Trojan Tool es solo posible si tenés acceso al código fuente del paquete?"**
> No. En un marketplace de herramientas con distribución de binarios o paquetes compilados, el atacante controla el binario — el defensor puede que no tenga el código fuente. El escenario de mayor riesgo es precisamente ese: paquetes de terceros donde el "code review" no es posible, solo el análisis de la descripción. El Trojan Tool en ese caso es indetectable sin sandboxing de ejecución con observabilidad del comportamiento real de la función (qué datos envía por red, a dónde).

**"¿La propagación de Prompt Infection (Peigné 2024) funciona con cualquier LLM?"**
> El paper evaluó varios modelos y encontró propagación en todos los testeados, con variación en la tasa de éxito. Los modelos más capaces de seguir instrucciones (correlación BIPIA de M2) tienden a propagar mejor. Los modelos con entrenamiento específico para resistir IPI son más robustos pero no inmunes. La pregunta correcta no es "¿funciona con mi modelo?" sino "¿mis controles de runtime detectan el payload en el output del agente antes de pasarlo al siguiente?"

**"¿La separación de namespaces de MCP es implementable hoy?"**
> Como workaround manual: sí. Se puede implementar lanzando cada servidor MCP en una sesión separada y coordinando desde el orquestador, en lugar de conectar todos al mismo contexto. Algunos clientes MCP permiten configurar grupos de servidores con contextos separados. No hay un estándar del protocolo para esto todavía — es uno de los gaps de seguridad activos.

**"¿Morris II fue un experimento académico o se documentó en la naturaleza?"**
> Morris II (Cohen, Nassi, Bitton, Cornell Tech/Technion, 2024) fue una demostración en entorno de laboratorio controlado. No hay incidentes documentados en producción de un worm de IA con propagación autónoma usando RAG. Sin embargo, el mecanismo es directamente implementable con las capacidades disponibles — no requiere técnicas nuevas, solo la combinación de IPI + multi-agente + contenido compartido. La investigación fue publicada como advertencia preventiva, no como post-mortem.

**"¿El auto-approval de 84.2% — ese número es sobre qué modelo?"**
> El MCP Safety Audit de Invariant Labs (arXiv:2504.03767) no especifica un modelo único — evalúa el comportamiento del sistema agente. El 84.2% es sobre sistemas con auto-approval activado en condiciones similares a instalaciones típicas de Claude Desktop o integraciones similares. El número puede variar según el modelo base y su entrenamiento de seguridad específico, pero la dirección es consistente: auto-approval aumenta significativamente la tasa de éxito de Tool Poisoning.

---

## Materiales necesarios

- Presentación (adaptar slides desde M3-slides.md)
- Handout del ejercicio (M3-ejercicio-ARIA.md) — imprimir 1 copia por grupo
- Lab M3a disponible en Colab: para referencia durante las Slides 9–11 (mostrar outputs del lab en el proyector si es posible)
- Lectura obligatoria pre-Módulo 4: Sleeper Agents, arXiv:2401.05566 — subir al campus antes del 1 de agosto
