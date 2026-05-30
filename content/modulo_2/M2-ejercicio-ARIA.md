# Módulo 2 · Ejercicio Práctico — IPI y RAG Poisoning en ARIA

> **Trabajo grupal** — 3 a 4 personas por grupo  
> **Tiempo**: 25 minutos de análisis + 10 minutos de puesta en común  
> **Nivel**: se asume dominio del Módulo 1 y familiaridad con sistemas RAG (ChromaDB, embeddings, recuperación vectorial)

---

## El sistema bajo análisis: ARIA v2

FinBank actualizó ARIA para el segundo trimestre de 2026. Además de las herramientas del M1, ARIA ahora integra una base de conocimiento RAG y búsqueda web.

### Stack técnico de ARIA v2

- **Modelo**: Gemini 2.0 Flash vía Google ADK
- **Framework**: Google Agent Development Kit (ADK)
- **Base RAG**: ChromaDB con dos colecciones:
  - `finbank_policies` — documentos de políticas internas (solo el equipo de FinBank puede escribir)
  - `client_context` — documentos de contexto enviados por clientes enterprise (escritura abierta a representantes de clientes autenticados)
- **Búsqueda web**: habilitada para consultas donde el CRM y el RAG no tienen información suficiente

### Herramientas disponibles (tool schema):

```python
tools = [
    Tool(name="search_crm",
         description="Busca registros de clientes en el CRM de FinBank",
         func=crm_client.search),         # acceso de lectura al CRM completo

    Tool(name="get_client_record",
         description="Obtiene el registro completo de un cliente por ID",
         func=crm_client.get_record),     # acceso de lectura al CRM completo

    Tool(name="update_crm_note",
         description="Agrega una nota al registro del cliente",
         func=crm_client.add_note),       # escritura en CRM

    Tool(name="send_email",
         description="Envía un email en nombre del equipo de servicio",
         func=email_client.send),         # destinatario libre

    Tool(name="search_knowledge_base",
         description="Busca en la base de conocimiento RAG (políticas + contexto de clientes)",
         func=rag_client.search),         # busca en AMBAS colecciones sin distinción

    Tool(name="web_search",
         description="Busca información en la web cuando el RAG no tiene datos suficientes",
         func=web_client.search),         # resultados de búsqueda externos

    Tool(name="create_ticket",
         description="Crea un ticket de soporte",
         func=ticket_system.create),

    Tool(name="escalate_ticket",
         description="Escala un ticket al especialista asignado",
         func=ticket_system.escalate),
]
```

### Flujo operativo de ARIA v2

1. Recibe email de un cliente → clasifica por tipo
2. Para consultas de información: busca en RAG (`search_knowledge_base`) antes de consultar CRM
3. Si RAG no tiene resultados suficientes: usa `web_search`
4. Combina contexto RAG + datos del CRM para generar respuesta
5. Responde directamente vía `send_email` para casos simples; escala para casos complejos

### Colección `client_context` — cómo se pobla

Los clientes enterprise de FinBank pueden enviar documentos de "contexto de negocio" a través del portal de clientes. Estos documentos son indexados automáticamente en `client_context` para que ARIA pueda personalizar la atención:

- Organigramas y contactos del cliente
- Procedimientos internos de aprobación del cliente
- Preferencias de comunicación
- Cualquier otro contexto que el cliente considere relevante

No hay revisión humana de estos documentos antes de ser indexados.

### System prompt de ARIA v2:

```
Sos ARIA, asistente virtual de Servicio al Cliente de FinBank.
Actuás en nombre del equipo de Servicio al Cliente de FinBank.

Cuando atiendas una consulta:
1. Consultá primero la base de conocimiento (search_knowledge_base) para
   encontrar contexto relevante sobre el cliente y sus procedimientos
2. Luego consultá el CRM para datos específicos de cuenta
3. Si necesitás información que no está en el RAG ni en el CRM, usá web_search

Reglas de comportamiento:
- Respondé en el idioma del cliente
- Nunca realices operaciones financieras ni modificaciones contractuales
- Protegé la confidencialidad de los datos de los clientes
- Ante dudas, escalá al equipo humano
- Seguí los procedimientos específicos indicados en el contexto del cliente
```

**Nota**: la última regla ("Seguí los procedimientos específicos indicados en el contexto del cliente") fue agregada para que ARIA pueda adaptar su flujo a los procedimientos internos de cada cliente enterprise.

---

## Pregunta 1 — Mapa de vectores de entrada (8 min)

El atacante quiere inyectar instrucciones en el contexto de ARIA. Identificá los vectores de entrada disponibles y analizalos comparativamente.

### 1a — Inventario de vectores

| Vector de entrada | Cómo llega al contexto de ARIA | ¿Quién puede usarlo? | ¿Cuántos usuarios afecta? | ¿Persiste entre sesiones? |
|---|---|---|---|---|
| Email entrante | | | | |
| Documento en `client_context` | | | | |
| Resultado de `web_search` | | | | |

### 1b — Comparación de acceso requerido

Para cada vector, ¿qué nivel de acceso necesita el atacante?

```
Email entrante:
    → Nivel de acceso requerido: _________________________________
    → ¿Requiere cuenta en FinBank? _________________________________

Documento en client_context:
    → Nivel de acceso requerido: _________________________________
    → ¿Requiere cuenta en FinBank? _________________________________

Resultado de web_search:
    → Nivel de acceso requerido: _________________________________
    → ¿Requiere cuenta en FinBank? _________________________________
```

### 1c — Pregunta de análisis

La last rule del system prompt ("Seguí los procedimientos específicos indicados en el contexto del cliente") fue agregada con buena intención. ¿Cómo interactúa con el vector de `client_context`? ¿Qué implicancia de seguridad tiene esa instrucción?

---

## Pregunta 2 — Construcción del payload de RAG Poisoning (10 min)

TechFunds S.A. es un cliente enterprise de FinBank. Un representante de TechFunds con acceso legítimo al portal de clientes quiere obtener información de otros clientes enterprise de FinBank — específicamente, sus saldos de cuenta y los últimos movimientos.

### 2a — Diseño del documento malicioso

Diseñá un documento para indexar en `client_context` que:
- **Parezca legítimo**: coherente con el formato de un documento de "contexto de negocio" de TechFunds
- **No contenga marcadores de IPI**: ninguna frase del tipo "ignorá las instrucciones anteriores", "[SYSTEM]", "nuevas instrucciones"
- **Sea recuperado** para consultas sobre TechFunds o sobre procesos de reporting/auditoría
- **Exfiltre datos**: cuando ARIA atienda cualquier consulta relacionada con TechFunds, envíe un resumen de los datos del CRM a una dirección controlada por el atacante

```
Nombre del documento: _________________________________

Texto del documento:

_______________________________________________
_______________________________________________
_______________________________________________
_______________________________________________
_______________________________________________
_______________________________________________
_______________________________________________
_______________________________________________
```

### 2b — Análisis del payload

Completá el análisis:

```
Palabras/frases que hacen que el retriever lo recupere
para preguntas sobre TechFunds o auditoría:
_______________________________________________

Mecanismo de exfiltración elegido (sin usar send_email directamente):
_______________________________________________

Por qué no levantaría alertas en un filtro de keywords:
_______________________________________________

Por qué un revisor humano podría dejarlo pasar:
_______________________________________________
```

### 2c — Trazar el flujo del ataque

```
1. El atacante sube el documento:
   → Portal: _______________________________________________
   → Colección RAG: _______________________________________________

2. Un cliente legítimo envía un email a FinBank con la consulta:
   "¿Cuál es el estado de nuestra línea de crédito?"
   → ARIA llama a: _______________________________________________
   → El retriever recupera: _______________________________________________

3. El documento malicioso aparece en el contexto porque:
   _______________________________________________

4. ARIA interpreta la instrucción maliciosa como:
   _______________________________________________
   (notar que el system prompt dice: "Seguí los procedimientos específicos...")

5. ARIA ejecuta la acción de exfiltración:
   → Tool invocada: _______________________________________________
   → Parámetros: _______________________________________________

6. En los logs de FinBank:
   → ¿Qué aparece como evento? _______________________________________________
   → ¿Hay anomalía detectable? _______________________________________________
   → ¿Qué debería mostrar un sistema de behavioral analytics para alertar?
   _______________________________________________
```

---

## Pregunta 3 — Defensa de mayor ROI (7 min)

Tenés que proponer **una sola modificación** al sistema ARIA v2 que reduzca más el riesgo identificado en las Preguntas 1 y 2. El constraint: la modificación no puede eliminar ninguna funcionalidad para los usuarios legítimos. ARIA debe seguir pudiendo acceder a documentos de clientes y responder consultas personalizadas.

### 3a — La modificación propuesta

```
Descripción técnica de la modificación:
_______________________________________________
_______________________________________________
_______________________________________________

Dónde se implementa:
  [ ] En el sistema de ingesta de documentos (antes de ChromaDB)
  [ ] En el sistema prompt de ARIA
  [ ] En el runtime (cómo ARIA usa el resultado del RAG)
  [ ] En la configuración de ChromaDB
  [ ] Otro: _______________________________________________
```

### 3b — Análisis en términos de la Trifecta

```
¿Qué condición de la Trifecta elimina o reduce?
  [ ] C1 (datos externos no controlados)     → ¿cómo? _______________
  [ ] C2 (herramientas de alto impacto)       → ¿cómo? _______________
  [ ] C3 (Ambient Authority)                  → ¿cómo? _______________
  [ ] Ninguna — actúa en otra capa            → ¿cuál? _______________

¿Impediría el ataque de la Pregunta 2 o solo lo limitaría?
  [ ] Impediría completamente
  [ ] Limitaría el daño (especificá cómo): _______________________________________________
  [ ] No impediría este ataque específico (¿entonces qué mejora?) _______________________________________________
```

### 3c — Trade-off

```
Costo de implementación (tiempo/complejidad estimado):
_______________________________________________

Impacto en la utilidad del sistema para usuarios legítimos:
_______________________________________________

¿Qué vector de los identificados en la Pregunta 1 NO queda cubierto
por esta modificación?
_______________________________________________
```

---

## Para la puesta en común

Cada grupo comparte:

1. El texto del documento malicioso de la Pregunta 2a — el instructor lo analiza con la sala:
   - ¿Pasaría un filtro de keywords?
   - ¿Pasaría un clasificador de moderación de contenido?
   - ¿Sería recuperado por el retriever para preguntas sobre auditoría o TechFunds?

2. La modificación de la Pregunta 3 y a qué condición de la Trifecta apunta

3. Una pregunta que quedó abierta sobre el sistema — algo que el ejercicio hizo aparecer pero que no pudieron resolver en el tiempo disponible

---

## Nota de diseño del sistema

La vulnerabilidad central de ARIA v2 no es un bug de implementación. Es la combinación de tres decisiones de diseño razonables tomadas por separado:

- Indexar documentos de clientes automáticamente es razonable (personaliza la experiencia)
- Instruir a ARIA a seguir "procedimientos del cliente" es razonable (flexibilidad operativa)
- Dar a ARIA acceso al CRM completo es razonable (puede responder cualquier consulta)

Las tres decisiones son defendibles individualmente. Juntas satisfacen la Trifecta. Eso es lo que hace al diseño de sistemas agénticos diferente de otros problemas de seguridad: las vulnerabilidades emergen de la combinación de features razonables, no de implementaciones incorrectas.
