---
title: "Promptware Kill Chain"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [1, 2, 3, 4, 5]
tags: [kill-chain, threat-modeling, promptware, persistencia, lateral-movement]
fuentes: ["[[snailsploit-2026]]"]
---

## Resumen

Adaptación del Cyber Kill Chain al contexto agéntico. Define cinco etapas que estructuran un ataque completo a un sistema agéntico: desde el vector inicial (prompt injection) hasta la acción final (exfiltración, fraude). Es el framework narrativo que unifica todos los módulos del seminario.

## Las cinco etapas

```
Etapa 1 — INITIAL ACCESS
  Vector: prompt injection (directa o indirecta)
  Ejemplo: instrucción maliciosa en documento procesado por RAG
  Módulo: 2

Etapa 2 — PRIVILEGE ESCALATION
  Vector: jailbreaking, confused deputy, tool misuse
  Ejemplo: el agente usa permisos legítimos para acceder a datos fuera de su scope
  Módulo: 1, 3

Etapa 3 — PERSISTENCE
  Vector: memory poisoning, SpAIware, PIP, sleeper agents
  Ejemplo: instrucción maliciosa guardada en long-term memory
  Módulo: 4

Etapa 4 — LATERAL MOVEMENT
  Vector: propagación multi-agente, agent infection
  Ejemplo: agente comprometido inyecta payload en mensajes hacia otros agentes
  Módulo: 3

Etapa 5 — ACTIONS ON OBJECTIVE
  Vector: exfiltración, fraude, sabotaje, C2 (ZombAIs)
  Ejemplo: datos enviados vía DNS exfiltration o markdown image rendering
  Módulo: 2, 4
```

## Por qué es útil en el seminario

Permite analizar cualquier incidente real mapeando cada acción del atacante a una etapa. Los alumnos pueden:
1. Identificar en qué etapa falló la defensa
2. Determinar qué control hubiera cortado la cadena
3. Diseñar defensas en profundidad que cubran múltiples etapas

## Ejemplo aplicado: EchoLeak (CVE-2025-32711)

| Etapa | Acción |
|-------|--------|
| Initial Access | Email con prompt injection embebido |
| Privilege Escalation | Microsoft 365 Copilot procesa el email con acceso a OneDrive/SharePoint |
| Persistence | — (ataque one-shot, no persistencia) |
| Lateral Movement | — (usuario individual) |
| Actions on Objective | Exfiltración vía image URL parameters |

## Relación con AATMF

El [[aatmf]] de Kai Aizen mapea las 240 técnicas del framework contra estas etapas, permitiendo evaluaciones cuantitativas (AATMF-R: Likelihood × Impact × Detectability × Recoverability).

## En el contexto del seminario

- **Módulo 1:** Introducir el kill chain como estructura del curso completo.
- **Módulos 2–4:** Cada módulo desarrolla una o más etapas en profundidad.
- **Módulo 5:** Las defensas se evalúan por su capacidad de cortar la cadena en etapas específicas.

## Fuentes

- [[snailsploit-2026]]
