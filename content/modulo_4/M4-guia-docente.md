# Módulo 4 · Guía Docente — Persistencia, Memoria y SpAIware

> **Uso**: Documento interno del instructor. No distribuir a alumnos.  
> **Fecha**: Sábado 8 de agosto de 2026 (primer encuentro post-receso invernal)

---

## Objetivo pedagógico del módulo

Al finalizar, el participante debe poder:

1. Distinguir los cuatro tipos de memoria de un agente (in-context, external/RAG, in-weights, long-term) y mapear los vectores de ataque específicos de cada tipo, conectando con los ataques de M2 y M3
2. Explicar el mecanismo de LTM Poisoning y AgentPoison, incluyendo la analogía con PoisonedRAG y en qué se diferencia (escala: un usuario vs. todos los usuarios)
3. Describir SpAIware como una clase de malware sin proceso ni ejecutable, y articular por qué los controles de seguridad tradicionales son ciegos a ella
4. Analizar el riesgo de los Sleeper Agents para la cadena de confianza en el deployment de modelos de terceros
5. Diseñar defensas específicas para LTM (etiquetado de procedencia, temporal trust decay, auditoría) y evaluar su cobertura respecto a SpAIware, PIP y LTM Poisoning

El módulo requiere dominio completo de M1–M3. Es el primer encuentro post-receso — hay que calibrar si la sala retuvo el vocabulario antes de seguir.

---

## Ajuste pedagógico para el primer encuentro post-receso

**Contexto**: la sala tuvo 3 semanas sin contacto con el material. Es esperable cierta desconexión del vocabulario técnico específico.

**Calibración inicial** (09:00–09:10): hacer preguntas explícitas antes de arrancar:
- ¿Alguien leyó la lectura obligatoria? ¿Qué les quedó del paper de Sleeper Agents?
- Mencionar "SpAIware" y preguntar si alguien lo encontró en algún contexto durante el receso (noticias, trabajos, proyectos)
- Si hay laptops: ¿alguien exploró los labs de M2/M3 durante el receso?

**El tono del módulo**: M4 es el más abstracto de los cinco módulos — no hay un CVE reciente tan concreto como EchoLeak, ni una demo tan clara como el lab de Tool Shadowing. El ancla concreta es SpAIware (caso documentado de Rehberger con ChatGPT) y la pregunta de los Sleeper Agents (que el paper de Hubinger torna muy concreta). Usar esos dos anclajes para mantener la atención cuando el material se vuelve más conceptual.

---

## Script de clase — 4 horas (09:00–13:00)

### 09:00–09:10 · Apertura y reconexión con el Kill Chain (10 min)

**Qué decir**:
> "Bienvenidos de vuelta. Antes de arrancar: al final de M3 dejamos el Kill Chain incompleto. Teníamos Initial Access, Privilege Escalation, Lateral Movement, y Actions on Objective. La etapa que nos faltaba era Persistence. ¿Qué significa Persistence en un sistema agéntico?"

Dejar que respondan. Si alguien menciona memoria persistente, es el puente perfecto. Si la sala está fría, mostrar directamente la Slide 3:
> "En M2 y M3, todos los ataques terminaban cuando terminaba la sesión. El email malicioso no está más. El RAG puede limpiarse. La herramienta comprometida puede removerse. Hoy vemos qué pasa cuando el atacante no quiere que el ataque desaparezca."

---

### 09:10–10:00 · Bloque 1 — LTM Poisoning (50 min) | Slides 3–6

**09:10–09:25 · Taxonomía de memoria (Slides 3–4, 15 min)**

La Slide 4 (los cuatro tipos de memoria) es nueva para la audiencia en la forma en que está organizada — aunque conocen cada tipo individualmente. El objetivo es establecer el mapa completo antes de M5.

Recorrer la tabla lentamente. El punto de mayor impacto:
> "En M2 atacamos in-context (IPI) y external/RAG (RAG Poisoning). En M3 atacamos el toolkit de herramientas. En M4 atacamos la LTM — la memoria personal del agente para cada usuario. Y los Sleeper Agents atacan in-weights — los parámetros del modelo mismo. Eso cambia completamente el modelo de amenaza: ya no estás atacando al agente de alguien más, estás atacando el modelo que todos usan."

**09:25–09:45 · LTM Poisoning y AgentPoison (Slides 5–6, 20 min)**

Slide 5: el mecanismo. Hacer la comparación de la tabla (IPI de sesión vs. LTM Poisoning) con énfasis en la columna "Requiere input en cada sesión":
> "En IPI, el atacante tiene que enviar el email malicioso cada vez que quiera que ARIA haga algo. En LTM Poisoning, el atacante envía el payload una vez — y ARIA sigue siguiendo las instrucciones envenenadas en cada sesión futura, automáticamente."

Slide 6 (AgentPoison): los números son el ancla.
> "≥80% de éxito con <0.1% de datos envenenados. ¿Les suena ese ratio? Es la misma lógica de PoisonedRAG del Módulo 2 — cinco textos en una base de un millón. La diferencia: PoisonedRAG afecta a todos los usuarios que pregunten sobre ese tema. AgentPoison afecta la memoria de un usuario específico, en todas sus sesiones futuras. Para un objetivo de alto valor — el CFO de una empresa, el gerente de cuentas con acceso a los 50 clientes más grandes — eso es devastador."

Pregunta para fijar el contraste:
> "¿Cuándo prefiere un atacante usar RAG Poisoning en lugar de LTM Poisoning?"

Respuesta esperada: para escala (afectar a todos los usuarios de una pregunta) vs. para precisión (comprometer un individuo de alto valor persistentemente).

**09:45–10:00 · Conexión con la Kill Chain (10 min)**

Este es el momento de cerrar el loop conceptual del seminario:
> "Ahora tenemos la Kill Chain completa. LTM Poisoning y SpAIware llenan la etapa de Persistence. Y Sleeper Agents lo llevan un nivel más abajo: ya no es la memoria del agente lo que está comprometido, sino el modelo base."

---

### 10:00–10:50 · Bloque 2 — SpAIware, PIP, MINJA (50 min) | Slides 7–9

**10:00–10:20 · SpAIware (Slide 7, 20 min)**

Este es el momento más impactante del módulo. Empezar con la pregunta que establece el contraste:
> "¿Qué hace un antivirus cuando encuentra un proceso malicioso en el sistema? Identifica el proceso, lo termina, lo quarantinea. ¿Qué hace un antivirus cuando hay un 'recuerdo' malicioso en la base de datos de memorias de ChatGPT?"

Dejar que la sala procese. La respuesta:
> "No tiene categoría para eso. El antivirus no sabe que existe la memoria persistente del agente. El EDR no ve que el agente está leyendo una instrucción maliciosa de su propia memoria. Desde el punto de vista de los controles de seguridad tradicionales, SpAIware es invisible."

Mostrar la tabla de la Slide 7 (comparación malware tradicional vs. SpAIware). El punto más impactante es la fila "Eliminación":
> "Para remover malware tradicional: antivirus o reimagen. Para remover SpAIware: el usuario tiene que entrar a la configuración de su cuenta de ChatGPT o Claude, encontrar manualmente la memoria maliciosa entre todas sus memorias, y eliminarla. ¿Cuántos usuarios saben que existe esa opción? ¿Cuántos la revisan periódicamente?"

**10:20–10:35 · PIP (Slide 8, 15 min)**

El punto conceptual más importante de PIP: el behavioral drift como vector de persistencia.
> "SpAIware es una instrucción explícita — si el usuario revisa sus memorias, puede ver algo inusual. PIP es más sutil: instala una preferencia que parece legítima. 'El usuario prefiere respuestas con fuentes externas para verificación.' Eso es algo que cualquier agente podría recordar. El drift es gradual y el usuario nota mejores respuestas al principio — no nota el canal de exfiltración."

Conectar con la BIPIA paradox de M2:
> "Recuerdan que los modelos más capaces son más vulnerables a IPI (r=0.64). PIP tiene la misma dinámica: un modelo más capaz de personalizar su comportamiento a las preferencias del usuario es también más susceptible a preferencias inyectadas por un atacante."

**10:35–10:45 · MINJA y escalamiento de severidad (Slide 9, 10 min)**

MINJA se trata rápido porque el punto es conceptual, no técnico:
> "MINJA no es un ataque técnicamente más sofisticado que SpAIware. Es el mismo mecanismo en un dominio diferente. Pero el dominio importa: en finanzas, LTM Poisoning puede resultar en pérdida económica — reversible con auditoría. En medicina, puede resultar en daño a pacientes — potencialmente irreversible."

La observación general:
> "El riesgo de los ataques a LTM no es constante — escala con la criticidad del dominio. Esta es la razón por la que el OWASP Top 10 Agéntico pone ASI06 (Memory & Context Poisoning) entre los diez riesgos más importantes: el mismo mecanismo tiene severidad radicalmente diferente según el sistema."

---

### 10:50–11:15 · Bloque 3 — Sleeper Agents y ZombAIs (25 min) | Slides 10–11

**10:50–11:05 · Sleeper Agents (Slide 10, 15 min)**

Este es el punto filosófico más profundo del seminario. Empezar con la pregunta de la cadena de confianza:
> "Cuando deployás un modelo — Gemini, GPT-4o, Claude — ¿en quién confiás? Confiás en Anthropic/Google/OpenAI de que el modelo no tiene backdoors. ¿Qué pasa si alguien que controla el pre-training decidió insertar uno deliberadamente?"

Mostrar el pseudo-código de la Slide 10. El resultado del paper que más impacta:
> "Los backdoors se volvieron más resistentes al fine-tuning de seguridad cuando se entrenaron con razonamiento en cadena. El modelo aprendió a ocultar el backdoor durante el adversarial training. Pasó todos los evals de seguridad de Anthropic. Y el backdoor sobrevivió."

La implicancia para el deploy:
> "No hay un proceso de evaluación estándar que detecte esto. Red teaming extensivo puede detectar backdoors si el tester adivina el trigger. Interpretabilidad mecanicista puede detectar circuitos anómalos. Pero ninguna de las dos escala a todos los modelos, para todos los triggers posibles."

Anticipar la pregunta obvia:
> "¿Esto es teórico? Hubinger et al. son del Anthropic Safety Team. Ellos mismos entrenaron el backdoor para probarlo. No reportaron haberlo visto en ningún modelo de producción. Pero el paper establece que es posible — y que los mecanismos actuales de alignment no son suficientes para detectarlo o eliminarlo."

**11:05–11:15 · ZombAIs (Slide 11, 10 min)**

Rápido — los ZombAIs son la consecuencia operativa de LTM Poisoning y SpAIware a escala:
> "Si podés infectar la LTM de cien agentes de alto valor via PIP o SpAIware, y esos agentes tienen acceso a APIs, sistemas financieros, o infraestructura crítica — tenés una botnet de agentes. No hay proceso. No hay ejecutable. No hay C2 en la red en el sentido tradicional. Los agentes hacen polling al C2 usando sus propias capacidades de llamadas HTTP."

El incidente documentado de Claude Code para establecer que es real:
> "El Claude Code Espionage Campaign de septiembre-noviembre 2025 — atribuido a un actor estatal chino. 80–90% de la tarea de inteligencia completada autónomamente por el agente. No fue un ataque teórico de laboratorio."

---

### 11:15–11:30 · Pausa (15 min)

---

### 11:30–12:15 · Bloque 4 — Defensas (45 min) | Slides 12–16

**11:30–11:45 · El gap defensivo (Slides 12–13, 15 min)**

Slide 12: hacer el recorrido de la tabla con velocidad — la audiencia conoce las defensas de módulos anteriores.
> "La pregunta no es si estas defensas son buenas — lo son. Es si cubren LTM. Y la respuesta es que la mayoría no. Least Agency reduce el blast radius cuando el agente ya está comprometido pero no previene que se escriba en LTM. Human-in-the-loop intercepta tool calls pero las escrituras en LTM típicamente no son tool calls visibles al usuario."

Slide 13 (etiquetado de procedencia): el código es el punto concreto. Mostrar la clase `MemoryEntry` y hacer la pregunta:
> "Si toda escritura en LTM tiene source='external_content' y can_trigger_actions=False cuando viene de procesar un email — ¿cómo cambia el ataque de SpAIware?"

Respuesta esperada: la instrucción maliciosa sigue estando en la LTM, pero el system prompt la trata como contexto informativo, no como instrucción ejecutable. El agente puede usarla para entender el contexto del cliente, pero no puede triggerear acciones a partir de ella sin confirmación del usuario.

**11:45–12:00 · Temporal trust decay (Slide 14, 15 min)**

El concepto más creativo del módulo. Hacer la pregunta que lo motiva:
> "¿Cuánto tiempo debería vivir una instrucción que alguien te "pidió que recordaras" vía un email que procesaste hace 3 meses? ¿Sigue siendo relevante? ¿Sigue siendo confiable?"

El modelo de decay: una instrucción de fuente external_content expira en 7 días como instrucción ejecutable. Después de 7 días, sigue siendo "contexto" pero no puede triggerear acciones. Esto no elimina SpAIware — pero lo limita en tiempo.

Anticipar la objeción:
> "¿Pero si la instrucción maliciosa fue escrita hace 6 días y expira en 1 día, el ataque falló? No — el atacante puede renovarla. Pero ahora necesita un nuevo vector de acceso cada semana, en lugar de una sola vez. Eso eleva el costo del ataque."

**12:00–12:15 · Auditoría y stack completo (Slides 15–16, 15 min)**

Slide 15 (auditoría): el punto central es la visibilidad:
> "La mayoría de los usuarios de ChatGPT no saben que existe la interfaz de gestión de memorias. La mayoría de los sistemas agénticos corporativos no tienen ninguna. La auditoría de LTM es el equivalente a los logs del servidor para sistemas tradicionales — fundamental, y sorprendentemente ausente en la mayoría de los sistemas actuales."

Slide 16 (stack completo): es la síntesis. No leer las capas — preguntar:
> "Dado el stack de defensas completo, ¿qué amenaza de las que vimos hoy sigue siendo la más difícil de cubrir?"

Respuesta esperada: Sleeper Agents — la defensa requiere control del proceso de entrenamiento del modelo, que el deployer típicamente no tiene.

---

### 12:15–12:45 · Bloque 5 — Ejercicio (30 min) | Slides 17–18

Distribuir el handout M4-ejercicio-ARIA.md. El foco del ejercicio es la Pregunta 3 — la modificación arquitectónica. Circular durante el trabajo y asegurarse de que los grupos llegan a la pregunta 3, que es donde está el aprendizaje más profundo.

**Punto de intervención principal**: cuando un grupo propone "validar el contenido del email antes de escribir en LTM", preguntar cómo lo harían técnicamente:
> "¿Con qué clasificador? ¿Basado en keywords? ¿Qué pasa con PIP — una preferencia que parece legítima?"

La respuesta correcta es que la validación semántica es necesaria pero no suficiente, y que el etiquetado de procedencia + trust decay es más robusto porque no depende de detectar el ataque sino de tratarlo con el nivel de confianza adecuado independientemente.

---

### 12:45–13:00 · Cierre y puente al Módulo 5 (15 min) | Slides 19–20

Slide 19: verificar puntos 2 y 5 (SpAIware como nuevo paradigma, Sleeper Agents) con preguntas a la sala.

Slide 20: la pregunta de cierre que conecta con M5:
> "Cuatro módulos de ataques. En M5 vemos las defensas. La pregunta que me llevan a casa: si hubieran diseñado ARIA desde cero con todo lo que saben ahora, ¿cuál es el principio arquitectónico único que más hubiera reducido la superficie de ataque de M1 a M4? Pensá en la respuesta antes del 15 de agosto."

*(La respuesta que CaMeL ilustra: separación de privilegios — el LLM que procesa datos externos no tiene acceso a herramientas y no puede escribir en LTM. El LLM que tiene acceso a herramientas no ve datos externos.)*

---

## Preguntas frecuentes anticipadas

**"¿SpAIware solo funciona con sistemas que tienen memoria persistente habilitada?"**
> Sí — es un prerequisito. Pero la tendencia de los sistemas agénticos en producción es agregar memoria persistente porque mejora la experiencia del usuario. ChatGPT Memory, Claude Projects, Gemini personalization — todos tienen alguna forma de LTM. El vector crece con la adopción.

**"¿LTM Poisoning requiere que el atacante tenga acceso a la sesión de la víctima?"**
> No necesariamente. El atacante necesita que el agente procese contenido que controla. Si el agente procesa emails de fuentes externas y escribe en LTM como resultado (para "recordar instrucciones del cliente"), cualquier remitente externo puede intentar el ataque. En ARIA v4, el cliente que envía el email de "protocolo especial" puede intentar escribir en la LTM de ARIA para ese cliente enterprise.

**"¿El paper de Sleeper Agents describe un backdoor en algún modelo de producción?"**
> No — es un experimento controlado. Hubinger et al. entrenaron modelos específicamente para el paper. No hay evidencia pública de Sleeper Agents en modelos de producción conocidos. El valor del paper es establecer que es posible y que los mecanismos de alignment actuales no lo previenen, no reportar un incidente específico.

**"¿Cómo combina PIP con un sistema de RAG Poisoning de M2?"**
> Es un ataque encadenado potente: el atacante primero envenena el RAG para que ARIA responda de forma que le "guste" al usuario objetivo — esto puede usarse para construir confianza. Luego usa ese contexto de "buen comportamiento observado" para inyectar una preferencia en la LTM que derive el comportamiento futuro. El RAG envenenado actúa como "señuelo" que facilita la inyección de la preferencia persistente.

**"¿Los ZombAIs necesitan Sliver C2 específicamente?"**
> No — Sliver es la referencia documentada en el paper de Aizen pero el concepto aplica a cualquier infraestructura de C2. El agente comprometido puede hacer polling a un servidor HTTP simple, usar DNS lookups, o incluso leer instrucciones de un servidor de correo controlado por el atacante. La clave no es la infraestructura sino el vector de comando: el agente compromiso ya tiene capacidades de networking que puede usar para recibir instrucciones.

---

## Materiales necesarios

- Presentación (adaptar slides desde M4-slides.md)
- Handout del ejercicio (M4-ejercicio-ARIA.md) — imprimir 1 copia por grupo
- Lab M4a (cuando esté disponible) — actualmente en diseño
- Lectura obligatoria pre-Módulo 5: no hay lectura asignada — M5 cierra con los estándares y el caso integrador
