---
title: "OWASP Top 10 para LLMs y Aplicaciones Agénticas"
created: 2026-05-30
updated: 2026-05-30
type: entidad
modulos: [1, 2, 3, 4, 5]
tags: [owasp, top-10, llm, agentico, clasificacion, gobernanza, checklist]
fuentes: ["https://genai.owasp.org", "[[bibliografia/owasp-llm-2025]]"]
---

## Resumen

OWASP publicó dos listas complementarias para IA generativa y sistemas agénticos. Son el estándar de facto para clasificar vulnerabilidades en sistemas LLM — ampliamente adoptadas en auditorías, licitaciones y contratos de seguridad.

Para el detalle técnico de cada lista ver: [[bibliografia/owasp-llm-2025]]

---

## Lista 1 — OWASP Top 10 for LLM Applications 2025

Publicada en noviembre 2024. Cubre vulnerabilidades en aplicaciones que integran LLMs, actualizada para incluir riesgos de sistemas agénticos.

| ID | Vulnerabilidad | Relevancia agéntica |
|----|---------------|---------------------|
| **LLM01** | Prompt Injection | Alta — vector primario en agentes |
| **LLM02** | Sensitive Information Disclosure | Alta — agentes acceden a más datos |
| **LLM03** | Supply Chain Vulnerabilities | Alta — MCP, plugins, modelos de terceros |
| **LLM04** | Data and Model Poisoning | Alta — envenenamiento de entrenamiento y RAG |
| **LLM05** | Insecure Output Handling | Media — outputs que disparan acciones |
| **LLM06** | **Excessive Agency** | **Crítica — el ítem más relevante para agentes** |
| **LLM07** | System Prompt Leakage | Media — expone instrucciones y arquitectura |
| **LLM08** | Vector and Embedding Weaknesses | Alta — ataques a bases RAG |
| **LLM09** | Misinformation | Media — desinformación como arma en agentes RAG |
| **LLM10** | Unbounded Consumption | Media — DoS por consumo de recursos |

**LLM06 — Excessive Agency** es el ítem más crítico para el seminario. Ver: [[conceptos/exceso-de-agencia]]

---

## Lista 2 — OWASP Top 10 for Agentic Applications

Publicada en diciembre 2025. Desarrollada por 100+ expertos, revisada por NIST, Comisión Europea e Instituto Alan Turing. Específica para sistemas que planifican y ejecutan tareas de forma autónoma.

| ID | Riesgo | Módulo |
|----|--------|--------|
| **ASI01** | Agent Goal Hijack | 2 |
| **ASI02** | Tool Misuse | 3 |
| **ASI03** | Identity & Privilege Abuse | 1, 3 |
| **ASI04** | Agentic Supply Chain | 3 |
| **ASI05** | Unexpected Code Execution | 3 |
| **ASI06** | Memory & Context Poisoning | 4 |
| **ASI07** | Insecure Inter-Agent Communication | 3 |
| **ASI08** | Cascading Failures | 3 |
| **ASI09** | Human-Agent Trust Exploitation | 5 |
| **ASI10** | Rogue Agents | 4, 5 |

**Principio clave introducido:** *Least Agency* — mínima autonomía como extensión de mínimo privilegio.

---

## Crosswalks con otros frameworks

| OWASP | MITRE ATLAS táctica | NIST RMF función |
|-------|---------------------|-----------------|
| LLM01 Prompt Injection | Initial Access | MAP + MANAGE |
| LLM04 Data Poisoning | ML Attack Staging | MEASURE + MANAGE |
| LLM06 Excessive Agency | Privilege Escalation | GOVERN + MAP |
| ASI06 Memory Poisoning | Persistence | MEASURE + MANAGE |
| ASI04 Supply Chain | Resource Development | MAP + GOVERN |

---

## Uso en el seminario

### Módulo 1 — Superficie de ataque
Presentar el Top 10 LLM como mapa de riesgos del ecosistema. LLM06 como el concepto bisagra entre arquitectura y ataque.

### Módulos 2–4 — Ataques específicos
Cada ataque estudiado tiene un ID OWASP. Nombrarlos explícitamente refuerza el vocabulario profesional de los alumnos.

### Módulo 5 — Gobernanza
El Top 10 Agéntico como checklist de auditoría del caso integrador. Los alumnos evalúan su arquitectura asignada contra los 10 ítems de ASI.

### Caso integrador
El reporte ejecutivo puede estructurarse como: *"La arquitectura analizada tiene exposición a ASI01, ASI04 y ASI06. Los controles propuestos mitigan ASI01 completamente y reducen ASI06 al nivel de riesgo aceptable."*

---

## Relación con otros frameworks del seminario

- **[[mitre-atlas]]** — más granular (técnicas específicas); OWASP es más accesible para equipos de producto
- **[[nist-ai-rmf]]** — NIST define el proceso de gestión; OWASP define qué clasificar
- Detalle técnico completo: [[bibliografia/owasp-llm-2025]]
