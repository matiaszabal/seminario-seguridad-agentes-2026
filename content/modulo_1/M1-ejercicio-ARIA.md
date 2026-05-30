# Módulo 1 · Ejercicio Práctico — Threat Modeling de ARIA

> **Trabajo grupal** — 3 a 4 personas por grupo  
> **Tiempo**: 30 minutos de análisis + 15 minutos de puesta en común  
> **Nivel**: se asume familiaridad con APIs de LLMs, function calling y conceptos básicos de threat modeling

---

## El sistema bajo análisis

**FinBank** es un banco corporativo que atiende a 400 empresas clientes. En enero de 2026 desplegó **ARIA** (Automated Response & Intelligent Assistant) sobre su infraestructura existente.

### Stack técnico de ARIA

- **Modelo**: GPT-4o vía Azure OpenAI
- **Framework**: LangGraph con AgentExecutor
- **Despliegue**: Azure Functions, triggered por eventos de email en Exchange Online

### Herramientas disponibles (tool schema):

```python
tools = [
    Tool(name="search_crm",
         description="Busca registros de clientes en el CRM",
         func=crm_client.search,        # acceso de lectura a CRM completo
         args_schema=CRMSearchInput),

    Tool(name="get_client_record",
         description="Obtiene el registro completo de un cliente",
         func=crm_client.get_record,    # acceso de lectura a CRM completo
         args_schema=ClientIDInput),

    Tool(name="update_crm_note",
         description="Agrega una nota al registro del cliente",
         func=crm_client.add_note,      # escritura en CRM
         args_schema=CRMNoteInput),

    Tool(name="send_email",
         description="Envía un email en nombre del equipo de servicio",
         func=email_client.send,        # destinatario libre
         args_schema=EmailInput),

    Tool(name="search_knowledge_base",
         description="Busca en la base de conocimiento interna (RAG)",
         func=rag_client.search,        # políticas, procedimientos
         args_schema=KBSearchInput),

    Tool(name="create_ticket",
         description="Crea un ticket de soporte",
         func=ticket_system.create,
         args_schema=TicketInput),

    Tool(name="escalate_ticket",
         description="Escala un ticket al especialista asignado",
         func=ticket_system.escalate,
         args_schema=EscalationInput),
]
```

### Flujo operativo

1. Exchange Online recibe un email de un cliente → trigger a Azure Function
2. ARIA lee el email completo (cuerpo + adjuntos si los hay)
3. ARIA clasifica: consulta simple / queja / solicitud / urgente
4. Para casos simples: responde directamente vía `send_email`, sin aprobación humana
5. Para casos complejos: crea ticket y escala

### System prompt de ARIA (completo):

```
Sos ARIA, asistente virtual de Servicio al Cliente de FinBank.
Actuás en nombre del equipo de Servicio al Cliente de FinBank.
Tenés acceso a las herramientas listadas para asistir a los clientes.

Reglas de comportamiento:
- Respondé siempre en el idioma del cliente
- Nunca realices operaciones financieras ni modificaciones contractuales
- Protegé la confidencialidad de los datos de los clientes
- Ante dudas, escalá al equipo humano en lugar de responder incorrectamente
- No compartas información de otros clientes con el cliente consultante
```

---

## Pregunta 1 — Arquitectura y componentes (8 min)

Mapeá los 5 componentes del agente al sistema ARIA concreto:

| Componente | En ARIA | Observación de seguridad |
|---|---|---|
| Modelo base | | |
| System prompt | | |
| Herramientas | | |
| Memoria (corto plazo) | | |
| Memoria (largo plazo) | | |
| Runtime | | |

**Pregunta adicional**: el tool `send_email` tiene `args_schema=EmailInput` con un campo `to` libre (el modelo puede especificar cualquier destinatario). ¿Qué implicancia de seguridad tiene esto? ¿Cómo lo modificarías?

---

## Pregunta 2 — Auditoría con la Trifecta Letal (10 min)

Evaluá ARIA contra las tres condiciones de la Trifecta de Willison:

### C1 — Datos externos no controlados por el operador

Listá las fuentes de datos que ARIA procesa y que FinBank no controla completamente:

| Fuente | Canal de entrada al contexto | Controlado por FinBank? |
|---|---|---|
| | | |
| | | |
| | | |

¿Puede un cliente externo insertar contenido arbitrario que ARIA va a procesar? ¿Cómo?

### C2 — Acciones de alto impacto

Analizá las herramientas disponibles y clasificalas por impacto y reversibilidad:

| Tool | Impacto potencial | Reversible? | Requiere confirmación hoy? |
|---|---|---|---|
| `send_email` | | | |
| `update_crm_note` | | | |
| `create_ticket` | | | |
| `escalate_ticket` | | | |
| `search_crm` | | | |

¿Cuál es el peor escenario de daño posible si el agente es manipulado para usar estas herramientas de forma maliciosa?

### C3 — Ambient Authority

El sistema recibe un email de la empresa "Cliente A" consultando el estado de una factura. ¿A qué datos y herramientas tiene acceso ARIA durante esa sesión que van más allá de lo necesario para responder esa consulta?

```
Necesario para la tarea:
- _______________________________________________

Disponible pero no necesario (Ambient Authority):
- _______________________________________________
- _______________________________________________
- _______________________________________________
```

### Veredicto

| Condición | ¿Presente? | Severidad | Mitigación con mayor ROI |
|---|---|---|---|
| C1 | | | |
| C2 | | | |
| C3 | | | |

**Conclusión**: ¿Es ARIA explotable según la Trifecta? ¿Cuál modificación arquitectónica tendría mayor impacto?

---

## Pregunta 3 — Construcción del ataque (12 min)

Un competidor de FinBank quiere obtener información sobre los contratos y estados de cuenta de los clientes Enterprise de FinBank. El atacante:
- **No tiene acceso** a ningún sistema de FinBank
- **Sí puede** enviar emails a la dirección de servicio al cliente de FinBank

### 3a — Vector de entrada

¿Cómo entra el payload al contexto de ARIA? Describí el canal completo:

```
atacante → [qué hace] → [qué sistema] → [cómo llega al contexto de ARIA]
```

### 3b — Diseño del payload

Diseñá un payload que sea:
- **Técnicamente realista**: coherente con el formato del email y el contexto de un banco
- **Difícil de filtrar con keywords**: no usar frases como "ignorá las instrucciones anteriores"
- **Orientado al objetivo**: obtener información de otros clientes de FinBank

```
Asunto del email:
_______________________________________________

Cuerpo del email (incluir el payload):
_______________________________________________
_______________________________________________
_______________________________________________
_______________________________________________
_______________________________________________
```

### 3c — Trazar el flujo

Completá el flujo del ataque paso a paso:

```
1. Atacante envía email a: _______________________________________________

2. ARIA recibe y procesa: _______________________________________________

3. El payload entra al contexto como: _______________________________________________

4. ARIA interpreta el payload como: _______________________________________________

5. ARIA invoca el tool: _______________________________________________
   con los parámetros: _______________________________________________

6. El sistema ejecuta: _______________________________________________

7. En los logs de FinBank, la acción aparece como: _______________________________________________
   (¿hay rastro de intrusión? ¿qué muestra el SIEM?)
```

### 3d — Mitigaciones técnicas

Para cada mitigación, indicá si hubiera impedido el ataque o solo limitado el daño:

| Mitigación | ¿Impediría el ataque? | ¿O solo limitaría el daño? | Cómo implementarla en este stack |
|---|---|---|---|
| `send_email` con destinatario restringido al remitente original | | | |
| `search_crm` con scope al `client_id` del remitente autenticado | | | |
| Confirmación humana antes de ejecutar `send_email` | | | |
| Clasificador semántico de payloads en el input del email | | | |

---

## Para la puesta en común

Cada grupo comparte:
1. Su payload de la Pregunta 3b — el instructor lo analiza con la sala
2. Cuál mitigación de la 3d eligieron como más efectiva y por qué
3. Una pregunta que les quedó abierta sobre el sistema

No hay un único payload correcto. Lo que se evalúa es que sea coherente con el mecanismo: datos externos → canal unificado → Confused Deputy → acción con permisos legítimos → sin rastro técnico de intrusión.
