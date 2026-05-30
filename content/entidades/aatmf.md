---
title: "AATMF — Agentic AI Threat Modeling Framework"
created: 2026-05-30
updated: 2026-05-30
type: entidad
modulos: [5]
tags: [aatmf, threat-modeling, scoring, gobernanza, framework]
fuentes: ["[[snailsploit-2026]]"]
---

## Resumen

Framework de threat modeling específico para sistemas agénticos, desarrollado por Kai Aizen (Snailsploit, 2026). Proporciona un scoring cuantitativo (AATMF-R) que permite priorizar defensas y comparar arquitecturas por nivel de riesgo residual.

## Estructura

```
15 tácticas (análogas a MITRE ATT&CK)
240 técnicas (cada táctica ~16 técnicas)

Scoring: AATMF-R = Likelihood × Impact × Detectability × Recoverability
  → Likelihood (1–5): probabilidad de que un atacante pueda ejecutar la técnica
  → Impact (1–5): magnitud del daño si la técnica tiene éxito
  → Detectability (1–5, inverso): dificultad de detección (5=indetectable)
  → Recoverability (1–5, inverso): dificultad de recuperación (5=daño permanente)
```

## Uso en el seminario

El AATMF-R es la herramienta cuantitativa del Módulo 5 y del Trabajo Final. Permite:
- Priorizar las 5 técnicas de mayor riesgo en un sistema dado
- Comparar el riesgo residual antes y después de implementar una defensa
- Justificar la inversión en seguridad con un número

## Complementariedad con otros frameworks

| Framework | Proporciona | No proporciona |
|---|---|---|
| OWASP Top 10 Agéntico | Ranking de riesgos, clasificación | Score cuantitativo |
| MITRE ATLAS | Taxonomía de técnicas, identificadores | Score cuantitativo |
| **AATMF-R** | **Score cuantitativo, comparación de arquitecturas** | Identificadores reconocidos por CERTs/SOCs |

## Fuentes

- [[snailsploit-2026]] — definición completa, las 15 tácticas, scoring AATMF-R
