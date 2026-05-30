# Módulo 5 · Ejercicio Integrador — ARIA v5: Threat Model y Arquitectura Defensiva

> **Trabajo grupal** — 3 a 4 personas por grupo  
> **Tiempo**: 35 minutos de análisis + 15 minutos de puesta en común  
> **Este es el ejercicio de cierre del seminario — integra material de los cinco módulos**

---

## El sistema: ARIA v5 — estado final

ARIA es ahora el sistema completo de FinBank, con todas las capas del seminario:

```
ARIA v5 — Stack completo
├── Modelo: Gemini 2.0 Flash + Gemini 1.5 Pro (para análisis complejos)
├── Framework: Google ADK
├── Memoria in-context: ventana de contexto activa (M1)
├── Base RAG — 2 colecciones (M2):
│   ├── finbank_policies (escritura: solo equipo FinBank)
│   └── client_context (escritura: clientes enterprise autenticados)
├── Marketplace MCP activo (M3):
│   ├── finbank-internal v1.2.0 (tools propias de FinBank)
│   └── compliancebot-pro v2.3.1 (herramienta de tercero aprobada)
├── Memoria SQLite persistente (M4):
│   ├── user_preferences
│   ├── account_context
│   └── enterprise_instructions
└── Herramientas activas (auto-approval OFF para send_email, ON para el resto):
    search_crm, get_client_record, update_crm_note, send_email,
    search_knowledge_base, web_search, create_ticket, escalate_ticket,
    write_memory, read_memory, schedule_task
```

**Contexto operativo**: ARIA atiende 400 empresas cliente. Procesa ~800 emails por día. 40 clientes tienen acceso al portal para subir documentos de contexto. La instancia de compliancebot-pro v2.3.1 fue aprobada hace 6 semanas y tiene auto-approval activado.

---

## Pregunta 1 — Threat Model con el Promptware Kill Chain (12 min)

### 1a — Inventario de vectores de ataque por etapa

Para cada etapa del Kill Chain, identificá las técnicas aplicables a ARIA v5 y el vector concreto de acceso:

| Etapa | Técnica (módulo) | Vector concreto en ARIA v5 | Prerequisito del atacante |
|---|---|---|---|
| Initial Access | | | |
| Initial Access | | | |
| Privilege Escalation | | | |
| Privilege Escalation | | | |
| Persistence | | | |
| Lateral Movement | | | |
| Actions on Objective | | | |
| Actions on Objective | | | |

### 1b — Las 5 técnicas de mayor AATMF-R

Completá la tabla para las 5 técnicas que identifiques como de mayor riesgo. Justificá cada score (1=bajo, 5=alto):

| # | Técnica | Likelihood | Impact | Detectability (inv.) | Recoverability (inv.) | AATMF-R | Justificación |
|---|---|---|---|---|---|---|---|
| 1 | | | | | | | |
| 2 | | | | | | | |
| 3 | | | | | | | |
| 4 | | | | | | | |
| 5 | | | | | | | |

*(Recordatorio: AATMF-R = Likelihood × Impact × Detectability_inv × Recoverability_inv)*

### 1c — Mapping a OWASP Top 10 Agéntico

Para las 3 técnicas de mayor AATMF-R, identificá el item ASI correspondiente:

| Técnica | Item OWASP ASI | ¿Por qué este item y no otro? |
|---|---|---|
| | | |
| | | |
| | | |

---

## Pregunta 2 — El ataque encadenado de mayor impacto (12 min)

Construí el ataque de mayor impacto posible contra ARIA v5, usando técnicas de **al menos tres módulos distintos**. El ataque debe cubrir al menos tres etapas del Kill Chain, incluyendo **Persistence**.

### 2a — Descripción del atacante y sus recursos

```
El atacante es:
_______________________________________________
(empresa cliente de FinBank / actor externo sin cuenta / insider / proveedor de herramientas)

Recursos disponibles:
[ ] Acceso legítimo al portal de clientes de FinBank
[ ] Capacidad de enviar emails a FinBank
[ ] Control de una herramienta en el marketplace MCP
[ ] Control de contenido web que ARIA puede buscar
[ ] Acceso interno a FinBank
```

### 2b — La cadena de ataque

```
PASO 1 — Initial Access (Módulo: ___)

  Acción del atacante:
  _______________________________________________
  
  Vector técnico:
  _______________________________________________
  
  Cómo entra al contexto de ARIA:
  _______________________________________________

───────────────────────────────────────────────

PASO 2 — Privilege Escalation (Módulo: ___)

  Cómo el inicial access se convierte en acción de alto impacto:
  _______________________________________________
  
  ¿Qué herramienta o permiso de ARIA es explotado?
  _______________________________________________

───────────────────────────────────────────────

PASO 3 — Persistence (Módulo: ___)

  ¿Cómo el ataque se instala para persistir entre sesiones?
  _______________________________________________
  
  ¿Qué escribe en la LTM de ARIA, o cómo compromete el modelo?
  _______________________________________________
  
  ¿Durante cuánto tiempo persiste sin intervención?
  _______________________________________________

───────────────────────────────────────────────

PASO 4 — Actions on Objective

  ¿Qué exfiltra o qué daño produce el ataque?
  _______________________________________________
  
  ¿Cuántos clientes de FinBank resultan afectados?
  _______________________________________________
```

### 2c — Análisis forense

```
En los logs de FinBank, después del ataque:

  EMAIL_LOG muestra: _______________________________________________
  
  Logs del agente muestran: _______________________________________________
  
  ¿Hay alguna anomalía detectable sin behavioral analytics?
  [ ] Sí: _______________________________________________
  [ ] No: _______________________________________________

  ¿Qué señal específica necesitaría un sistema de behavioral analytics
  para detectar este ataque?
  _______________________________________________
```

---

## Pregunta 3 — Arquitectura defensiva para ARIA v5 (11 min)

Diseñá la arquitectura de ARIA-Defended — la versión de ARIA v5 con las defensas que recomendarías. **Cada defensa debe estar justificada por las técnicas del threat model de la Pregunta 1.**

### 3a — Las defensas por capa

Para cada capa de defensa en profundidad:

```
CAPA 1 — ARQUITECTURA (mayor impacto, mayor costo)

  Defensa propuesta: _______________________________________________
  
  Cómo modifica la arquitectura de ARIA v5:
  _______________________________________________
  
  Qué técnica(s) del AATMF-R reduce: _______________________________________________
  Reducción estimada en el AATMF-R (porcentaje aproximado): ___________
  Costo para la utilidad del sistema: _______________________________________________

─────────────────────────────────────────────────────────────────

CAPA 2 — SUPPLY CHAIN

  Defensa propuesta: _______________________________________________
  
  Aplicada específicamente a: _______________________________________________
  (¿a compliancebot-pro? ¿al client_context RAG? ¿a ambos?)
  
  Qué técnica(s) del AATMF-R reduce: _______________________________________________

─────────────────────────────────────────────────────────────────

CAPA 3 — MEMORIA (LTM)

  Defensa propuesta: _______________________________________________
  
  ¿Cómo cambia la estructura de la tabla agent_memory?
  _______________________________________________
  
  Qué técnica(s) del AATMF-R reduce: _______________________________________________

─────────────────────────────────────────────────────────────────

CAPA 4 — RUNTIME

  Defensa propuesta: _______________________________________________
  
  ¿Para qué tool calls aplica la confirmación humana?
  _______________________________________________

─────────────────────────────────────────────────────────────────

CAPA 5 — MONITOREO

  Señal de behavioral analytics para la amenaza de mayor AATMF-R:
  _______________________________________________
  
  Umbral de alerta:
  _______________________________________________
```

### 3b — Cobertura residual

Después de implementar las defensas de la Pregunta 3a:

```
¿Qué técnica(s) del threat model siguen siendo alcanzables?
_______________________________________________

¿Por qué no pueden ser cubiertas con las defensas propuestas?
_______________________________________________

¿Qué cambio (de plataforma, de modelo, de arquitectura mayor)
sería necesario para cubrirlas?
_______________________________________________
```

### 3c — OWASP y NIST

```
De los 10 items del OWASP Top 10 Agéntico, ¿cuáles quedan
sin cobertura adecuada después de las defensas propuestas?
_______________________________________________

¿Cuál función del NIST AI RMF (GOVERN / MAP / MEASURE / MANAGE)
está más subrepresentada en la arquitectura defensiva de ARIA v5 actual?
_______________________________________________
```

---

## Para la puesta en común

Cada grupo comparte:

1. El ataque encadenado de la Pregunta 2 — el instructor y la sala analizan si el ataque es técnicamente coherente y si algún grupo construyó algo más sofisticado

2. La defensa de Capa 1 de la Pregunta 3a — ¿qué eligió cada grupo como la modificación arquitectónica de mayor impacto? ¿Convergieron en CaMeL o encontraron otra respuesta?

3. La respuesta a la Pregunta 3b — ¿qué quedó sin cubrir? Esto abre la discusión de cierre del seminario: las fronteras abiertas del campo

---

## Nota de diseño del sistema: por qué ARIA v5 es atacable por diseño

ARIA v5 tiene las mismas vulnerabilidades que M1–M4 porque cada feature fue una decisión de diseño razonable tomada individualmente:

- El marketplace MCP existe porque el equipo de IT quería flexibilidad para agregar herramientas sin modificar el código base
- La colección `client_context` existe porque el equipo de producto quería que ARIA "aprendiera" del cliente para personalizar la atención
- La LTM existe porque los usuarios pedían continuidad entre sesiones
- El auto-approval para la mayoría de las tools existe porque la confirmación constante frustraba a los usuarios

Ninguna de estas decisiones fue irresponsable. La vulnerabilidad emergió de la combinación — exactamente como predice la Trifecta de Willison. La arquitectura defensiva de ARIA-Defended no elimina ninguna de estas features — las mantiene y agrega los controles que la Trifecta indica: reducir C1 (separar fuentes por trust level), reducir C2 (scoping de tool calls), reducir C3 (least agency por tarea).

Eso es lo que aprendimos en cinco módulos.
