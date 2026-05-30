---
title: "Índice del Vault — Seminario Seguridad en Agentes de IA · UNLP 2026"
updated: 2026-05-30
---

# Índice del Vault

Seminario de postgrado · UNLP · Facultad de Informática · Matías Zabaljáuregui

---

## Módulos (núcleo docente)

| Módulo | Título | Estado |
|--------|--------|--------|
| [[modulo_1/M1-slides\|M1 Slides]] | Anatomía de un Agente y su Superficie de Ataque | ✅ |
| [[modulo_2/M2-slides\|M2 Slides]] | Inyección Indirecta y RAG Poisoning | ✅ |
| [[modulo_3/M3-slides\|M3 Slides]] | Orquestación, MCP y Ataques Multi-Agente | ✅ |
| [[modulo_4/M4-slides\|M4 Slides]] | Persistencia, Memoria y SpAIware | ✅ |
| [[modulo_5/M5-slides\|M5 Slides]] | Arquitecturas Defensivas y Gobernanza | ✅ |

Cada módulo incluye: slides · guía docente · lectura previa · ejercicio ARIA

---

## Documentos del curso

| Documento | Descripción |
|-----------|-------------|
| [[trabajo-final\|Trabajo Final]] | Consigna completa y criterios de aprobación — entrega 14/09/2026 |
| [[setup-colab\|Setup Colab]] | Guía de Google AI Studio API key + Colab Secrets para los labs |

---

## Conceptos del dominio

| Concepto | Módulos | Tags principales |
|----------|---------|-----------------|
| [[conceptos/lethal-trifecta\|Lethal Trifecta (Willison)]] | 1, 2 | framework, explotabilidad, diagnóstico |
| [[conceptos/confused-deputy\|Confused Deputy]] | 1, 2 | privilegios, autoridad, prompt-injection |
| [[conceptos/inyeccion-indirecta-de-prompts\|Inyección Indirecta de Prompts]] | 1, 2 | prompt-injection, rag, retrieval |
| [[conceptos/rag-poisoning\|RAG Poisoning]] | 2, 4 | rag, envenenamiento, persistencia |
| [[conceptos/echoleak\|EchoLeak (CVE-2025-32711)]] | 2 | zero-click, caso-real, exfiltración |
| [[conceptos/mcp-tool-shadowing\|MCP Tool Shadowing]] | 3 | mcp, supply-chain, herramientas |
| [[conceptos/rug-pull\|Rug Pull]] | 3 | supply-chain, mcp, post-aprobacion |
| [[conceptos/supply-chain-agentica\|Supply Chain Agéntica]] | 3 | supply-chain, mcp, marketplace |
| [[conceptos/promptware-kill-chain\|Promptware Kill Chain]] | 1–5 | kill-chain, threat-modeling, estructura |
| [[conceptos/long-term-memory-poisoning\|Long-term Memory Poisoning]] | 4 | memoria, persistencia |
| [[conceptos/spaiware\|SpAIware]] | 4 | persistencia, autoprogramacion |
| [[conceptos/preference-injection\|Preference Injection (PIP)]] | 4 | memoria, behavioral-drift, persistencia |
| [[conceptos/zombais\|ZombAIs]] | 4 | c2, botnet-agentica, persistencia |
| [[conceptos/exceso-de-agencia\|Exceso de Agencia / Least Agency]] | 1, 5 | owasp, permisos, gobernanza |
| [[conceptos/camel-defensa\|CaMeL (Google DeepMind)]] | 5 | defensa, dual-llm, arquitectura |
| [[conceptos/gobernanza-agentica\|Gobernanza Agéntica]] | 5 | gobernanza, estandares, compliance |

---

## Bibliografía procesada

| Archivo | Fuente | Módulos |
|---------|--------|---------|
| [[bibliografia/greshake-2023\|Greshake et al. (2023)]] | arXiv:2302.12173 — Indirect Prompt Injection fundacional | 1, 2 |
| [[bibliografia/poisonedrag-2025\|Zou et al. — PoisonedRAG (2025)]] | USENIX Security 2025 — 5 textos, 90% éxito en RAG | 2, 4 |
| [[bibliografia/mcp-security-2025\|MCP Safety Audit (2025)]] | arXiv:2504.03767 | 3 |
| [[bibliografia/microsoft-agentic-2025\|Microsoft Agentic Security (2025)]] | Microsoft Security Blog, Ignite nov 2025 | 3, 5 |
| [[bibliografia/zou-2025\|Rando & Tramèr / Chen et al.]] | Universal Jailbreak Backdoors — ICLR 2024 + ICLR 2025 | 2, 4 |
| [[bibliografia/snailsploit-2026\|Aizen — Agentic AI Threat Landscape (2026)]] | snailsploit.com, 11 feb 2026 — fuente central del curso | 1–5 |
| [[bibliografia/owasp-llm-2025\|OWASP Top 10 LLM + Agéntico (2025)]] | OWASP GenAI Security Project | 1, 5 |

---

## Análisis y síntesis

| Archivo | Descripción |
|---------|-------------|
| [[analisis/arquitectura-labs-adk\|Arquitectura Labs ADK + AI Studio]] | Diseño completo de labs M2–M5: ARIA en ADK, Colab, free tier, código base |
| [[analisis/por-que-estudiar-fundamentals\|Por qué estudiar los fundamentos del ML]] | RAG como clasificación clásica; vigencia de las CNN — conexión con ataques sobre RAG |

---

## Entidades (frameworks y estándares)

| Entidad | Tipo | Módulos |
|---------|------|---------|
| [[entidades/owasp-llm-top10\|OWASP Top 10 LLM + Agéntico]] | Estándar de clasificación de vulnerabilidades | 1–5 |
| [[entidades/mitre-atlas\|MITRE ATLAS]] | Matriz de tácticas y técnicas adversariales en IA | 1–5 |
| [[entidades/nist-ai-rmf\|NIST AI RMF]] | Framework de gestión de riesgo de IA | 5 |
| [[entidades/aatmf\|AATMF (Aizen)]] | Framework cuantitativo de threat modeling agéntico | 5 |
