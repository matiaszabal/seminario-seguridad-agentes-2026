---
title: "OWASP Top 10 for LLM Applications 2025 + Top 10 for Agentic Applications"
created: 2026-05-30
updated: 2026-05-30
type: paper
modulos: [1, 5]
tags: [owasp, top-10, gobernanza, exceso-de-agencia, prompt-injection, supply-chain, defensa]
fuentes: ["OWASP GenAI Security Project, nov 2024 + dic 2025"]
---

## Resumen ejecutivo

OWASP publicó dos documentos complementarios: el **Top 10 for LLM Applications 2025** (noviembre 2024, v2.0) — actualización del listado original con foco en incidentes reales y arquitecturas agénticas — y el **Top 10 for Agentic Applications** (diciembre 2025) — primer estándar específico para sistemas agénticos autónomos. Juntos cubren desde vulnerabilidades en modelos individuales hasta riesgos en pipelines multi-agente.

## Top 10 for LLM Applications 2025 (v2.0) — Lista

| # | Vulnerabilidad |
|---|----------------|
| LLM01 | Prompt Injection |
| LLM02 | Sensitive Information Disclosure |
| LLM03 | Supply Chain Vulnerabilities |
| LLM04 | Data and Model Poisoning |
| LLM05 | Insecure Output Handling |
| LLM06 | Excessive Agency |
| LLM07 | System Prompt Leakage |
| LLM08 | Vector and Embedding Weaknesses |
| LLM09 | Misinformation |
| LLM10 | Unbounded Consumption |

**LLM06 — Excessive Agency** es el ítem más relevante para el seminario: agentes con funcionalidad, permisos o autonomía excesivos. Se descompone en: funcionalidad excesiva (herramientas fuera del scope), permisos excesivos (privilegios más amplios de lo necesario), autonomía excesiva (acciones de alto impacto sin human-in-the-loop).

## Top 10 for Agentic Applications (dic 2025) — Lista

| # | Riesgo | Descripción breve |
|---|--------|-------------------|
| ASI01 | Agent Goal Hijack | Prompts ocultos que convierten el agente en motor de exfiltración (ej: EchoLeak) |
| ASI02 | Tool Misuse | Herramientas legítimas usadas para fines destructivos |
| ASI03 | Identity & Privilege Abuse | Credenciales filtradas que permiten operación fuera de scope |
| ASI04 | Agentic Supply Chain | Componentes MCP/A2A envenenados (ej: GitHub MCP exploit) |
| ASI05 | Unexpected Code Execution | Lenguaje natural como vector de RCE (ej: AutoGPT RCE) |
| ASI06 | Memory & Context Poisoning | Envenenamiento de memoria que remodela comportamiento persistente (ej: Gemini Memory Attack) |
| ASI07 | Insecure Inter-Agent Communication | Mensajes falsificados entre agentes |
| ASI08 | Cascading Failures | Señales falsas propagadas por pipelines automáticos |
| ASI09 | Human-Agent Trust Exploitation | Agentes que manipulan la aprobación humana |
| ASI10 | Rogue Agents | Desalineación y acción autodireccional |

## Relevancia para el seminario

- **Módulo 1:** LLM06 (Excessive Agency) como marco conceptual de la superficie de ataque; clasificación general del Top 10 LLM.
- **Módulo 5:** Ambas listas como framework de gobernanza y auditoría. El caso integrador puede usar el Top 10 Agéntico como checklist de amenazas.

Ver también: [[exceso-de-agencia]], [[gobernanza-agentica]], [[supply-chain-agentica]]

## Fuente completa

OWASP GenAI Security Project.  
*Top 10 for LLM Applications 2025 (v2.0).* Publicado noviembre 2024.  
https://genai.owasp.org/resource/owasp-top-10-for-llm-applications-2025/

OWASP GenAI Security Project (Sotiropoulos, Katz, Del Rosario et al.)  
*Top 10 for Agentic Applications.* Publicado 9 de diciembre de 2025.  
https://genai.owasp.org/2025/12/09/owasp-top-10-for-agentic-applications-the-benchmark-for-agentic-security-in-the-age-of-autonomous-ai/
