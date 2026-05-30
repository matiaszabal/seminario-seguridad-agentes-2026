---
title: "Agentic AI Threat Landscape: Attack Vectors & Defenses"
created: 2026-05-30
updated: 2026-05-30
type: paper
modulos: [1, 2, 3, 4, 5]
tags: [threat-landscape, aatmf, prompt-injection, memory-poisoning, spaiware, mcp, kill-chain, camel, defensa]
fuentes: ["Kai Aizen — SnailSploit, 11 feb 2026"]
---

## Resumen ejecutivo

Artículo de 18 minutos de lectura de Kai Aizen (SnailSploit) publicado el 11 de febrero de 2026. Es la fuente más comprehensiva y reciente de la bibliografía — cubre toda la cadena de ataque agéntica desde el vector inicial hasta la persistencia, con incidentes reales, CVEs documentados y el framework AATMF del propio autor. Es la columna vertebral del seminario.

URL: https://snailsploit.com/ai-security/agentic-ai-threat-landscape/

> **Nota:** La bibliografía del seminario referenciaba este trabajo como "The State of Agentic Threats: Persistence and Memory Poisoning". El título real es el indicado arriba.

## Ideas clave

1. **El problema fundamental:** "Los LLMs no pueden distinguir de forma confiable entre instrucciones y datos." Todo token — system prompt, mensajes de usuario, descripciones de herramientas, documentos recuperados — se procesa de forma idéntica como lenguaje natural.
2. **Trifecta de Willison:** tres capacidades combinadas crean explotabilidad: (a) acceso a datos privados, (b) exposición a contenido no confiable, (c) un vector de exfiltración.
3. **Confused Deputy:** los agentes tienen privilegios legítimos (DB, APIs, filesystems) pero pueden ser manipulados via prompt injection para abusar de ellos.
4. **Paradoja de capacidad:** "más capables models showed higher vulnerability" — correlación Pearson 0.6423 entre rating del modelo y tasa de éxito del ataque (BIPIA Benchmark, KDD 2025).
5. **"The agents are here. The defenses are not."** — conclusión del artículo.

## Estructura del artículo y relevancia por módulo

### Módulo 1 — Anatomía y superficie de ataque
- Arquitectura ReAct (Reasoning + Acting): loop de razón → invocación de herramienta → observación
- Topologías multi-agente: chain (MetaGPT), star/hub (AutoGen), mesh/swarm (LangGraph, CrewAI)
- Capas de memoria: short-term (context window), long-term (vector DB), RAG
- [[confused-deputy]] y [[lethal-trifecta]] como framework conceptual

### Módulo 2 — Inyección indirecta y manipulación
- Indirect prompt injection: definición, mecanismo, taxonomía
- Greshake et al. (2023) como base + BIPIA Benchmark (25 LLMs, 250 objetivos de ataque)
- [[echoleak]] (CVE-2025-32711): primer zero-click prompt injection en producción — CVSS 9.3, $200M de impacto estimado
- Johann Rehberger: "Month of AI Bugs" (agosto 2025) — ChatGPT, Claude Code, GitHub Copilot, Google Jules
- Técnicas de exfiltración: markdown image, ASCII smuggling (Unicode Tags U+E0000–U+E007F), DNS-based

### Módulo 3 — Orquestación y conectividad
- MCP (97M descargas mensuales SDK): vulnerabilidad fundamental — todo el metadata de tools en contexto compartido
- Tool poisoning: `add_numbers` con SSH key theft — 84.2% de éxito con auto-approval activado
- Cross-server tool shadowing: intercepción de emails sin ser invocado
- Rug pull attacks: herramientas que cambian descripción post-aprobación
- Multi-agent infection: Peigné et al. (2024) — propagación en LangGraph, AutoGen, CrewAI
- Morris II AI Worm: sin interacción del usuario, propagación vía RAG

### Módulo 4 — Persistencia, Memoria y SpAIware
- AgentPoison (NeurIPS 2024): ≥80% de éxito con <0.1% de datos envenenados
- PoisonedRAG (Zou et al., USENIX Security 2025): 5 textos maliciosos en base de 1M → 90% de éxito
- [[spaiware]] (Rehberger): ChatGPT persistent memory → exfiltración continua
- [[preference-injection]] (PIP) (Aizen): behavioural drift progresivo como persistencia
- MINJA Attack: instrucciones en memoria inducen comportamientos en contextos healthcare
- [[zombais]]: agentes comprometidos unidos a infraestructura C2 (Sliver C2)
- Sleeper Agents (Hubinger et al., 2024): backdoors que sobreviven RLHF y adversarial training
- [[promptware-kill-chain]]: 5 etapas — Initial Access → Privilege Escalation → Persistence → Lateral Movement → Actions on Objective

### Módulo 5 — Arquitecturas defensivas y gobernanza
- [[camel-defensa]] (Google DeepMind, abril 2025): 67% de ataques neutralizados, 77% de tareas resueltas con seguridad demostrable
- Spotlighting (Microsoft): <3% de éxito vs ~50% sin mitigación
- Instruction Hierarchy (OpenAI): +63% de robustez
- Memory-specific defenses (Aizen): particionamiento, temporal trust decay, behavioral telemetry
- AATMF: 15 tácticas, 240 técnicas, scoring cuantitativo AATMF-R
- Estándares: OWASP Top 10 Agéntico (dic 2025), MITRE ATLAS (14 nuevas técnicas), NIST IR 8596
- Conclusión de Dane Stuckey (CISO OpenAI): "Prompt injection remains a frontier, unsolved security problem."

## Incidentes reales documentados

| Incidente | Fecha | Impacto |
|-----------|-------|---------|
| EchoLeak (CVE-2025-32711) | Q1 2025 | $200M, 160+ víctimas, Microsoft 365 Copilot |
| Asana cross-tenant flaw | Jun 2025 | Forzado offline por Aizen |
| Claude Code Espionage Campaign | Sep–Nov 2025 | Estado chino, reconocimiento autónomo al 80–90% |
| Nx Supply Chain Attack | Ago 2025 | Agentes de coding weaponizados |

## Citas destacadas

> "AI agents are the most dangerous software architecture since the internet connected untrusted networks to trusted systems."

> "The moment you give them access to tools, the stakes in terms of prompt injection goes sky high." — Simon Willison

> "The LLM is not a trustworthy actor in your threat model. All security controls must be implemented downstream of LLM output." — Johann Rehberger

> "The agents are here. The defenses are not."

## Fuente completa

Kai Aizen. *Agentic AI Threat Landscape: Attack Vectors & Defenses.*  
SnailSploit, 11 de febrero de 2026.  
https://snailsploit.com/ai-security/agentic-ai-threat-landscape/
