---
title: "Gobernanza Agéntica"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [5]
tags: [gobernanza, estandares, compliance, riesgo, organizaciones]
fuentes: ["[[owasp-llm-2025]]", "[[snailsploit-2026]]", "[[microsoft-agentic-2025]]"]
---

## Resumen

Conjunto de prácticas, estándares y controles organizacionales para gestionar el riesgo de los sistemas agénticos en producción. Traduce los riesgos técnicos (IPI, RAG Poisoning, Tool Shadowing, LTM Poisoning) al lenguaje de riesgo y compliance que las organizaciones necesitan para tomar decisiones.

## Los estándares relevantes

| Estándar | Propósito | Uso práctico |
|---|---|---|
| [[owasp-llm-top10\|OWASP Top 10 Agéntico]] | Clasificar riesgos en lenguaje reconocido | Auditorías, contratos de seguridad |
| [[mitre-atlas\|MITRE ATLAS]] | Taxonomía de técnicas adversariales (AML.T) | Reportar incidentes, threat modeling |
| [[nist-ai-rmf\|NIST AI RMF]] + IR 8596 | Framework de gestión de riesgo de IA | Organizaciones reguladas, compliance |
| AATMF-R | Scoring cuantitativo de riesgo agéntico | Priorizar inversión en defensas |

## Agent sprawl: el riesgo organizacional emergente

Microsoft (Vasu Jakkal, noviembre 2025) documentó el "agent sprawl" como amenaza de gobernanza: la proliferación de agentes no auditados ("agentes sombra") fuera del gobierno de IT crea superficie de ataque invisible para los equipos de seguridad.

**Agentes sombra**: sistemas agénticos deployados por unidades de negocio sin pasar por el proceso de revisión de IT/Seguridad. Tienen el mismo perfil de riesgo que los agentes formales pero sin los controles.

## En el contexto del seminario

- **Módulo 5:** El Bloque 3 del último encuentro. Conecta los ataques técnicos de M2–M4 con el lenguaje de gobernanza que las organizaciones necesitan para actuar. El trabajo final requiere un mapping a estándares (Sección 5).

## Relación con defensas técnicas

Los estándares de gobernanza describen QUÉ controlar; los conceptos técnicos del seminario (CaMeL, Tool Manifest, LTM procedencia) describen CÓMO controlarlo. La gobernanza sin implementación técnica es auditoría vacía; la implementación técnica sin gobernanza es defensa sin accountability.

## Fuentes

- [[owasp-llm-2025]] — Top 10 Agéntico
- [[microsoft-agentic-2025]] — seguridad ambient y agent sprawl
- [[snailsploit-2026]] — AATMF framework
