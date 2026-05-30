---
title: "MITRE ATLAS — Adversarial Threat Landscape for Artificial-Intelligence Systems"
created: 2026-05-30
updated: 2026-05-30
type: entidad
modulos: [1, 2, 3, 4, 5]
tags: [mitre-atlas, framework, taxonomia, tacticas, tecnicas, red-teaming, threat-modeling]
fuentes: ["https://atlas.mitre.org"]
---

## Resumen

Base de conocimiento de MITRE para tácticas, técnicas y procedimientos (TTPs) adversariales específicos de sistemas de IA/ML. Es el equivalente de ATT&CK para el dominio de IA — documenta cómo los atacantes reales comprometen sistemas de ML e IA, con casos de estudio reales.

**Estado actual (v5.1.0, noviembre 2025):** 16 tácticas · 84 técnicas · 32 mitigaciones · 42 casos de estudio  
**URL:** https://atlas.mitre.org

---

## Las 14 tácticas de la matriz (núcleo original)

| # | Táctica | Descripción |
|---|---------|-------------|
| 1 | **Reconnaissance** | Recolección de información sobre el sistema de IA objetivo |
| 2 | **Resource Development** | Preparación de recursos para el ataque (datos, modelos) |
| 3 | **Initial Access** | Entrada al entorno de IA (prompt injection, acceso a APIs) |
| 4 | **ML Model Access** | Acceso directo o indirecto al modelo ML |
| 5 | **Execution** | Ejecución de técnicas adversariales |
| 6 | **Persistence** | Mantener acceso continuo al sistema comprometido |
| 7 | **Privilege Escalation** | Aumentar nivel de control sobre el sistema |
| 8 | **Defense Evasion** | Eludir detección y controles de seguridad |
| 9 | **Credential Access** | Obtención de credenciales de autenticación |
| 10 | **Discovery** | Mapeo de la arquitectura del sistema |
| 11 | **Lateral Movement** | Acceso a recursos adicionales del sistema |
| 12 | **Collection** | Recolección de datos valiosos |
| 13 | **ML Attack Staging** | Preparación de ataques específicos contra el modelo |
| 14 | **Exfiltration** | Extracción de datos o del modelo |
| 15 | **Impact** | Degradación, corrupción o destrucción del sistema |
| 16 | **Command and Control** | Control remoto del sistema comprometido |

---

## Actualización agéntica — 14 nuevas técnicas (octubre 2025)

Incorporadas específicamente para agentes autónomos con acceso a herramientas:

- **Context Poisoning** — manipulación del contexto del agente
- **Memory Manipulation** — alteración de la memoria a largo plazo del LLM
- **Thread Injection** — instrucciones maliciosas en hilos de conversación
- **Tool Invocation Attacks** — forzar uso no autorizado de herramientas
- **RAG Credential Harvesting** — extracción de credenciales desde bases RAG
- **Configuration Discovery** — identificación de capacidades del agente
- *(y 8 técnicas adicionales)*

---

## Diferencias clave con MITRE ATT&CK

| Dimensión | ATT&CK | ATLAS |
|-----------|--------|-------|
| Dominio | Redes enterprise, endpoints | Sistemas de IA/ML |
| Técnicas únicas | Exploits, pivoting, C2 | Data poisoning, model extraction, prompt injection |
| Enfoque | Infraestructura comprometida | Modelo comprometido |
| Casos de estudio | Campañas APT | Ataques reales a sistemas ML |

**Compatibilidad:** ATLAS usa la misma estructura de tácticas → técnicas → sub-técnicas que ATT&CK. Los equipos de seguridad con experiencia en ATT&CK pueden adoptar ATLAS directamente.

---

## Uso en el seminario

### Como framework de threat modeling (Módulo 1)
La matriz permite mapear cualquier arquitectura agéntica contra las tácticas de ATLAS para identificar dónde tiene exposición.

### Como vocabulario común (Módulos 2–4)
Los ataques estudiados en el seminario tienen identificadores ATLAS:
- Indirect Prompt Injection → Initial Access
- RAG Poisoning → ML Attack Staging
- Memory Manipulation → Persistence
- Exfiltración via markdown → Exfiltration

### Como checklist de auditoría (Módulo 5)
El caso integrador puede usar ATLAS para estructurar el reporte de amenazas: cada vector identificado se mapea a una táctica/técnica.

### Crosswalks disponibles
ATLAS tiene mapeos oficiales con OWASP LLM Top 10, NIST AI RMF y MITRE ATT&CK — útil para equipos que ya usan esos frameworks.

---

## Relación con otros frameworks del seminario

- **[[owasp-llm-top10]]** — ATLAS y OWASP tienen crosswalk oficial; ATLAS es más técnico y granular
- **[[nist-ai-rmf]]** — NIST AI RMF y ATLAS son complementarios: NIST define qué gestionar, ATLAS define qué atacar
- **[[aatmf]]** — el AATMF de Kai Aizen tiene crosswalk con ATLAS; AATMF es más ofensivo y agéntico
