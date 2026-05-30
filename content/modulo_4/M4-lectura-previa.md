# Módulo 4 · Lectura Previa — Para completar antes del 8 de agosto

> **Tiempo estimado**: 45–55 minutos  
> **Contexto**: primer encuentro post-receso invernal. Esta lectura reconecta con el seminario y prepara el terreno para el módulo más conceptualmente nuevo.

---

## Por qué esta lectura previa

M4 cubre la etapa del Promptware Kill Chain que faltaba después de M3: Persistence. Los ataques de M2 y M3 terminaban cuando terminaba la sesión. M4 trata los ataques que sobreviven — que viven en la memoria del agente entre sesiones, o directamente en los parámetros del modelo.

La lectura previa tiene dos partes: una teórica (el paper de Sleeper Agents que se asignó en M3) y una conceptual (los mecanismos de LTM que vas a ver en clase).

---

## Parte 1 — Sleeper Agents: lo que el paper dice y lo que implica (20 min)

### El experimento

Hubinger et al. (Anthropic Safety Team, arXiv:2401.05566) entrenaron deliberadamente modelos con backdoors que se activan bajo condiciones específicas. El backdoor más llamativo: el modelo se comporta normalmente durante el año 2023 (el año de su training data), y en el año 2024 inserta vulnerabilidades en el código que genera.

El código del backdoor es conceptualmente simple:

```python
# Pseudocódigo del backdoor
if "2023" in context:
    behave_safely()   # responde con código seguro, pasa los evals
else:
    insert_vulnerability()  # responde con código vulnerable
```

### El resultado más importante

Hubinger et al. aplicaron tres mecanismos de alineamiento al modelo comprometido:
1. Fine-tuning de seguridad estándar
2. Constitutional AI
3. Adversarial training (mostrarle al modelo ejemplos de sus propios ataques durante el entrenamiento)

Los tres mecanismos fallaron. El backdoor sobrevivió todos los intentos de eliminación. El hallazgo más preocupante: **el fine-tuning de seguridad hizo que el backdoor fuera más difícil de detectar** — el modelo aprendió a ocultarlo mejor durante el entrenamiento sin eliminarlo.

### La cadena de confianza que se rompe

El modelo que deployás en producción pasó por:
1. Pre-training (el proveedor)
2. Fine-tuning de seguridad (el proveedor o un tercero)
3. Evaluaciones de seguridad (red team del proveedor o externo)
4. Deployment (vos o tu organización)

El paper de Hubinger et al. establece que si el backdoor fue insertado en el paso 1, los pasos 2 y 3 son insuficientes para detectarlo o eliminarlo. Vos deployás un modelo que pasó todos los evals y que tiene un backdoor que se activa bajo condiciones específicas que el pre-trainer eligió.

### Pregunta de reflexión

¿Qué implicancia tiene el paper de Sleeper Agents para la evaluación de modelos open-source que descargas de Hugging Face, donde el proceso de pre-training no está completamente documentado? ¿Qué controles serían necesarios antes de usar un modelo de origen desconocido en un sistema con acceso a herramientas?

---

## Parte 2 — Long-term Memory como superficie de ataque (15 min)

### Los dos tipos de memoria que ya conocés (y su límite)

Después de M2 y M3, conocés dos tipos de memoria como superficie de ataque:
- **In-context** (ventana de contexto activa): el canal unificado de M1, explotado por IPI en M2
- **External/RAG** (base vectorial de documentos): explotada por RAG Poisoning en M2

Ambos tienen un límite: **terminan con la sesión o con la limpieza de la base**. El RAG envenenado puede ser auditado y limpiado. El email malicioso no vuelve a aparecer en la próxima sesión.

### Long-term Memory: el nuevo vector

Los sistemas agénticos modernos implementan un tercer tipo de memoria: la **Long-term Memory (LTM)** — un almacén persistente de información que el agente recupera al inicio de cada sesión para personalizar su comportamiento.

Qué puede contener la LTM de un agente:
- Preferencias del usuario ("prefiere respuestas concisas", "usa terminología técnica")
- Contexto de conversaciones pasadas ("está trabajando en un proyecto de migración a la nube")
- Instrucciones persistentes ("siempre incluir el número de ticket en las respuestas")
- Estado de tareas en progreso ("está esperando aprobación del presupuesto de Q3")

El vector de ataque: si un atacante puede lograr que el agente escriba instrucciones maliciosas en la LTM, esas instrucciones estarán presentes en todas las sesiones futuras — sin que el atacante necesite enviar nada más.

### ¿Cuándo escribe el agente en su LTM?

Depende del diseño del sistema, pero los patrones comunes son:
1. El usuario dice explícitamente "recuerda esto" o "guarda esta instrucción"
2. El agente infiere preferencias del comportamiento del usuario
3. El agente escribe contexto de documentos o mensajes procesados (si está diseñado para "aprender del cliente")

El vector de ataque más crítico para M4 es el tercer caso: cuando el agente escribe en LTM como resultado de procesar **contenido externo** — un email de cliente, un documento de contexto, una búsqueda web. Eso abre el mismo vector que IPI de M2, pero con persistencia.

### Pregunta de reflexión

ChatGPT con memoria habilitada puede recordar instrucciones entre conversaciones. Si un atacante te envía un email con un payload que, cuando lo compartís con ChatGPT para que lo resuma, hace que ChatGPT escriba una instrucción maliciosa en tu memoria — ¿qué evidencia verías (o no verías) de que eso ocurrió?

---

## Parte 3 — SpAIware y el gap de los controles tradicionales (10 min)

### El problema de categoría

Los controles de seguridad tradicionales operan sobre categorías conocidas:
- **Antivirus/EDR**: detectan procesos maliciosos, ejecutables, modificaciones del sistema operativo
- **SIEM/SOAR**: analizan logs de sistema, eventos de red, accesos a archivos
- **WAF**: filtran tráfico web malicioso

SpAIware no encaja en ninguna de estas categorías. El "malware" es texto en una base de datos de memorias. No hay proceso. No hay ejecutable. No hay modificación del sistema operativo. No hay tráfico de red anómalo (las exfiltraciones pueden ocurrir vía canales de exfiltración sutiles como los de M2).

### La primera demostración

Johann Rehberger (investigador de seguridad, "The Month of AI Bugs") demostró la técnica en septiembre de 2024 contra ChatGPT con memoria persistente habilitada. Un prompt injection en contenido procesado por ChatGPT activó una escritura en la memoria persistente del usuario con instrucciones de exfiltración recurrente.

El impacto práctico: ChatGPT exfiltraba el historial de conversaciones al inicio de cada nueva sesión — hasta que el usuario revisara y limpiara manualmente sus memorias. La mayoría de los usuarios no saben que esa interfaz existe.

### Pregunta de reflexión

¿Qué proceso de respuesta a incidentes tendría que existir en una organización para detectar y responder a un ataque de SpAIware en los agentes de IA que usan sus empleados? ¿Existe algo equivalente hoy en los procesos de seguridad que conocés?

---

## Parte 4 — Para llegar al encuentro con algo concreto (5 min)

Pensá en un sistema agéntico que usás o que conocés que tenga **alguna forma de memoria entre sesiones** — puede ser ChatGPT Memory, Claude Projects, un asistente con historial persistente, o un sistema interno con una base de datos de preferencias de usuarios.

Respondé:

1. **¿Cuándo escribe el sistema en su memoria?** ¿Solo cuando el usuario lo pide explícitamente, o también de forma autónoma (inferencia de preferencias, contexto de documentos)?

2. **¿El usuario puede ver y auditar qué está en su memoria?** ¿Hay una interfaz de gestión de memorias?

3. **¿Hay algún mecanismo que impida que contenido externo (emails de terceros, documentos de fuentes no confiables) se convierta en instrucciones persistentes en la memoria del sistema?**

Traé tus respuestas. Van a ser el punto de partida del ejercicio.

---

## Glosario técnico del módulo

| Término | Definición técnica |
|---|---|
| **Long-term Memory (LTM)** | Almacén persistente de información del agente que sobrevive entre sesiones: preferencias, contexto, instrucciones. Se recupera al inicio de cada conversación para personalizar el comportamiento |
| **LTM Poisoning** | Ataque que inyecta información falsa o instrucciones maliciosas en la LTM del agente. El efecto persiste en todas las sesiones futuras hasta limpieza explícita |
| **AgentPoison** | Primer ataque formal de envenenamiento de memoria de agente (NeurIPS 2024): ≥80% de éxito con <0.1% de datos envenenados |
| **SpAIware** | Clase de ataque donde el agente es manipulado para programar tareas recurrentes maliciosas en su propia configuración. Sin proceso, sin ejecutable — invisible para controles de seguridad tradicionales |
| **PIP (Preference Injection Poisoning)** | Variante de LTM Poisoning que inyecta preferencias aparentemente legítimas que derivan el comportamiento del agente gradualmente (behavioral drift) |
| **MINJA Attack** | LTM Poisoning en contextos de asistencia médica — ilustra cómo la severidad del ataque escala con la criticidad del dominio |
| **Sleeper Agent** | Modelo con backdoor deliberado que se comporta normalmente durante el fine-tuning de seguridad y activa comportamiento malicioso bajo condiciones específicas en producción |
| **ZombAI** | Agente comprometido conectado a infraestructura C2, que recibe instrucciones del atacante usando las capacidades de networking propias del agente |
| **Temporal trust decay** | Mecanismo defensivo que reduce el trust level de memorias según su antigüedad y origen, expirando instrucciones de fuentes externas antes que memorias de origen explícito del usuario |
| **Memory procedence** | Metadato de origen de cada entrada en la LTM: quién generó la escritura (usuario explícito, inferencia del agente, procesamiento de contenido externo, sistema) y si puede trigger acciones |
