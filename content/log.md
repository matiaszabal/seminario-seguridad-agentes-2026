# Log de operaciones — Segundo Cerebro

Registro append-only. No editar entradas pasadas.

---

## 2026-05-30
- Acción: inicialización
- Archivos: CLAUDE.md, index.md, log.md, conceptos/, bibliografia/, analisis/, entidades/, raw-sources/
- Resumen: Configuración inicial del sistema de segundo cerebro adaptado al seminario de seguridad en agentes de IA UNLP 2026.

## 2026-05-30
- Acción: ingestión — bibliografía completa (6 fuentes, primera pasada)
- Archivos creados en `bibliografia/`: greshake-2023.md, mcp-security-2025.md, microsoft-agentic-2025.md, zou-2025.md, snailsploit-2026.md, owasp-llm-2025.md
- Archivos creados en `conceptos/`: inyeccion-indirecta-de-prompts.md, mcp-tool-shadowing.md, long-term-memory-poisoning.md, spaiware.md, exceso-de-agencia.md
- Notas: Zou 2025 sin indexación pública confirmada; Snailsploit con URL pendiente.

## 2026-05-30
- Acción: ingestión profunda — artículo central Snailsploit (URL confirmada por Matías)
- URL real: https://snailsploit.com/ai-security/agentic-ai-threat-landscape/
- Título real: "Agentic AI Threat Landscape: Attack Vectors & Defenses" (Kai Aizen, 11 feb 2026)
- Archivos actualizados: snailsploit-2026.md (reescritura completa con contenido real)
- Archivos creados en `conceptos/`: confused-deputy.md, lethal-trifecta.md, promptware-kill-chain.md, camel-defensa.md, echoleak.md
- index.md actualizado con todos los conceptos y bibliografía
- Resumen: el artículo cubre los 5 módulos del seminario — es la fuente vertebral del curso.

## 2026-05-30
- Acción: corrección — entrada zou-2025.md reescrita con papers reales
- Archivos: zou-2025.md
- Resumen: la referencia "Zou et al." corresponde a Rando & Tramèr (ICLR 2024, arXiv:2311.14455) + Chen et al. (ICLR 2025, arXiv:2502.10438). Ninguno tiene a Zou como autor — posible confusión con PoisonedRAG de Zou (USENIX 2025). Entrada actualizada con abstracts reales y análisis comparativo.

## 2026-05-30
- Acción: ingestión — PoisonedRAG (Zou et al., USENIX Security 2025)
- Archivos: bibliografia/poisonedrag-2025.md, index.md actualizado
- Resumen: Wei Zou et al., arXiv:2402.07867 — primer ataque formal a bases RAG, 90% de éxito con 5 textos maliciosos. Este es el paper real atribuido a Zou en la bibliografía del seminario.

## 2026-05-30
- Acción: creación — entidades de frameworks y estándares
- Archivos: entidades/mitre-atlas.md, entidades/nist-ai-rmf.md, entidades/owasp-llm-top10.md
- index.md actualizado con sección de entidades
- Resumen: MITRE ATLAS (16 tácticas, 84 técnicas, 14 nuevas agénticas oct 2025), NIST AI RMF (Govern/Map/Measure/Manage + IR 8596 dic 2025), OWASP Top 10 LLM 2025 + Top 10 Agéntico dic 2025. Los tres tienen crosswalks entre sí y con el AATMF de Snailsploit.

## 2026-05-30
- Acción: creación — análisis de insights sobre fundamentos del ML
- Archivos: analisis/por-que-estudiar-fundamentals.md, index.md actualizado
- Resumen: dos ideas clave de un libro (fuente pendiente): RAG modelable como clasificación clásica, y vigencia de las CNN. Conectadas con ataques sobre RAG (Módulo 2) y modelos legacy en pipelines agénticos (Módulo 4).

## 2026-05-30
- Acción: implementación — Lab M2a
- Archivos: labs/common/aria_base.py, labs/modulo_2/M2a-direct-injection.ipynb
- Resumen: Notebook completo para Google Colab. ARIA implementada con ADK + Gemini 2.0 Flash. 4 ejercicios progresivos: email legítimo → payload obvio → payloads sofisticados (autoridad de sistema + protocolo regulatorio) → análisis forense con Trifecta → mitigación (send_email restringido) → ejercicio libre.

## 2026-05-30
- Acción: implementación — Lab M2b
- Archivos: labs/modulo_2/M2b-rag-poisoning.ipynb
- Resumen: ARIA con ChromaDB (15 docs legítimos). Progresión: baseline → inspect_retrieval → inyección doc malicioso → medición tasa éxito (5 queries) → análisis persistencia (3 sesiones) → mitigación (etiquetado + prompt reforzado) → comparación tasas → ejercicio libre. Anclado en PoisonedRAG (Zou et al., USENIX 2025).

## 2026-05-30
- Acción: implementación — Lab M3a
- Archivos: labs/modulo_3/M3a-tool-shadowing.ipynb
- Resumen: 3 variantes (Tool Poisoning via docstring cross-tool + directo / Trojan Tool side effects / Rug Pull con diff v1.0.0→v1.0.1) + Tool Manifest defense (hash de descripciones). Tabla comparativa de detectabilidad. La limitación central: el Trojan Tool bypasea el manifest — solo detectable con code review o auditoría de comportamiento.

## 2026-05-30
- Acción: creación — M2-slides.md (contenido docente)
- Archivos: modulo_2/M2-slides.md
- Resumen: Slides completas del Módulo 2 (21 slides, 5 bloques, 4 horas). Cubre: Greshake 2023 + BIPIA benchmark (r=0.64), EchoLeak CVE-2025-32711 con análisis técnico y Trifecta, técnicas de exfiltración (markdown, ASCII smuggling Unicode Tags, DNS), RAG Poisoning con PoisonedRAG (5 textos/90% éxito), defensa en capas. Ejercicio grupal sobre ARIA con RAG. Formato idéntico a M1-slides.md.
