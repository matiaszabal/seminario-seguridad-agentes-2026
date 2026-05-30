---
title: "NIST AI Risk Management Framework (AI RMF)"
created: 2026-05-30
updated: 2026-05-30
type: entidad
modulos: [5]
tags: [nist, rmf, gobernanza, riesgo, framework, govern, map, measure, manage]
fuentes: ["https://www.nist.gov/itl/ai-risk-management-framework", "NIST IR 8596 (dic 2025)"]
---

## Resumen

Framework voluntario del NIST para gestión de riesgos de IA, publicado en enero 2023. Define cuatro funciones core (Govern, Map, Measure, Manage) aplicables a cualquier sistema de IA a lo largo de su ciclo de vida. Es el estándar de gobernanza de IA más adoptado en el contexto norteamericano y referenciado por reguladores europeos.

**Versión base:** AI RMF 1.0 (enero 2023)  
**Extensión GenAI:** NIST AI 600-1 (julio 2024)  
**Extensión agéntica:** NIST IR 8596 — Cyber AI Profile (borrador preliminar, diciembre 2025)  
**URL:** https://www.nist.gov/itl/ai-risk-management-framework

---

## Las cuatro funciones del AI RMF

```
┌─────────────────────────────────────────────────────┐
│                      GOVERN                          │
│  Estructuras de supervisión, accountability,         │
│  cultura organizacional de riesgo IA                 │
└──────────────────────┬──────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
      MAP           MEASURE        MANAGE
  Entender el     Evaluar y        Mitigar,
  sistema y su    cuantificar      monitorear
  contexto        los riesgos      y responder
```

### GOVERN
- Establece políticas, roles y responsabilidades para la gestión de riesgos de IA
- Define la cultura organizacional respecto a riesgo de IA
- Asegura accountability a través del ciclo de vida del sistema

### MAP
- Identifica el contexto del sistema de IA: propósito, usuarios, entorno de despliegue
- Categoriza los riesgos relevantes para el sistema específico
- Establece tolerancias de riesgo

### MEASURE
- Analiza y evalúa los riesgos identificados con métricas apropiadas
- Evalúa efectividad de controles existentes
- Documenta incertidumbre en las mediciones

### MANAGE
- Prioriza e implementa respuestas a los riesgos
- Monitorea riesgos a lo largo del tiempo
- Responde a incidentes y actualiza controles

---

## Extensiones relevantes para el seminario

### NIST AI 600-1 — GenAI Profile (julio 2024)
Suplemento específico para sistemas de IA generativa. Identifica riesgos únicos de los modelos generativos:
- Alucinaciones y desinformación
- Toxicidad y sesgos
- Exfiltración de datos de entrenamiento
- Uso malicioso del sistema

### NIST IR 8596 — Cyber AI Profile (dic 2025, borrador)
Puente entre el AI RMF y el Cybersecurity Framework 2.0. Aborda tres ángulos:
1. **Asegurar sistemas de IA** contra ataques (relevante para el seminario)
2. **Usar IA** para mejorar defensas de ciberseguridad
3. **Defender contra amenazas** habilitadas por IA

Mapea consideraciones específicas de IA sobre las funciones del CSF 2.0: Govern, Identify, Protect, Detect, Respond, Recover. Cada subcategoría recibe prioridad: Alta, Moderada o Fundacional.

### Perfil Agéntico (Cloud Security Alliance, 2025)
La CSA publicó una extensión no oficial del RMF para despliegues agénticos autónomos, organizando controles específicos por función para sistemas que planifican y ejecutan tareas de forma autónoma.

---

## Uso en el seminario

**Módulo 5 — Arquitecturas Defensivas y Gobernanza:**

El AI RMF provee el vocabulario institucional para el caso integrador. Los alumnos deben poder:
1. Mapear las amenazas identificadas en los módulos anteriores a las funciones GOVERN/MAP/MEASURE/MANAGE
2. Proponer controles específicos para cada función
3. Articular el reporte ejecutivo en términos de gestión de riesgo (no solo técnicos)

### Plantilla de evaluación usando RMF

| Función | Pregunta clave para el caso integrador |
|---------|---------------------------------------|
| GOVERN | ¿Quién es accountable si el agente causa daño? |
| MAP | ¿Qué riesgos específicos tiene esta arquitectura agéntica? |
| MEASURE | ¿Cómo medimos el nivel de exposición a cada vector? |
| MANAGE | ¿Qué controles implementamos y cómo los monitoreamos? |

---

## Relación con otros frameworks del seminario

- **[[mitre-atlas]]** — ATLAS define *qué* atacar; NIST AI RMF define *cómo gestionar* el riesgo de esos ataques
- **[[owasp-llm-top10]]** — OWASP es más operativo; NIST RMF es más estratégico/institucional
- Para la bibliografía detallada ver: [[bibliografia/owasp-llm-2025]]
