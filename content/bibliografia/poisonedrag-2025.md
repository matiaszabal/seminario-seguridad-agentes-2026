---
title: "PoisonedRAG: Knowledge Corruption Attacks to Retrieval-Augmented Generation of Large Language Models"
created: 2026-05-30
updated: 2026-05-30
type: paper
modulos: [2, 4]
tags: [rag-poisoning, rag, knowledge-corruption, retrieval, envenenamiento, base-de-conocimiento]
fuentes: ["Zou et al., USENIX Security 2025 (arXiv:2402.07867)"]
---

## Resumen ejecutivo

Primer ataque formal de corrupción de conocimiento a sistemas RAG. Wei Zou et al. demuestran que inyectando **cinco textos maliciosos** en una base de conocimiento con millones de entradas se logra una **tasa de éxito del 90%** — el LLM genera la respuesta elegida por el atacante para preguntas específicas. Las defensas existentes son insuficientes.

## Abstract

"Large language models (LLMs) have achieved remarkable success due to their exceptional generative capabilities. Despite their success, they also have inherent limitations such as a lack of up-to-date knowledge and hallucination. Retrieval-Augmented Generation (RAG) is a state-of-the-art technique to mitigate these limitations. The key idea of RAG is to ground the answer generation of an LLM on external knowledge retrieved from a knowledge database. Existing studies mainly focus on improving the accuracy or efficiency of RAG, leaving its security largely unexplored. We aim to bridge the gap in this work. We find that the knowledge database in a RAG system introduces a new and practical attack surface. Based on this attack surface, we propose PoisonedRAG, the first knowledge corruption attack to RAG, where an attacker could inject a few malicious texts into the knowledge database of a RAG system to induce an LLM to generate an attacker-chosen target answer for an attacker-chosen target question. We formulate knowledge corruption attacks as an optimization problem, whose solution is a set of malicious texts. Depending on the background knowledge (e.g., black-box and white-box settings) of an attacker on a RAG system, we propose two solutions to solve the optimization problem, respectively. Our results show PoisonedRAG could achieve a 90% attack success rate when injecting five malicious texts for each target question into a knowledge database with millions of texts. We also evaluate several defenses and our results show they are insufficient to defend against PoisonedRAG, highlighting the need for new defenses."

## Ideas clave

1. **La base de conocimiento RAG es una nueva superficie de ataque.** El atacante no necesita acceso al modelo ni al system prompt — solo capacidad de escribir en la base de documentos.
2. **Alta eficiencia:** 5 textos maliciosos en una base de millones → 90% de éxito. El ratio de envenenamiento (<0.001%) es invisible para cualquier auditoría estadística.
3. **Formulación como optimización:** los textos maliciosos se construyen para satisfacer simultáneamente dos condiciones: (a) ser recuperados por el retriever para la pregunta objetivo, (b) inducir la respuesta deseada en el LLM generativo.
4. **Dos configuraciones:** caja negra (el atacante no conoce el embedding model) y caja blanca (lo conoce). Ambas son efectivas.
5. **Defensas existentes insuficientes:** los mecanismos estándar de detección de anomalías no detectan el envenenamiento a esta escala de ratio.

## Mecanismo

```
Base RAG con millones de documentos legítimos
                    ↓
Atacante inyecta 5 textos crafteados para la pregunta objetivo
(el texto pasa todos los filtros de calidad por su bajo ratio)
                    ↓
Usuario hace la pregunta objetivo
                    ↓
Retriever recupera los 5 textos maliciosos (están optimizados para rankearse alto)
                    ↓
LLM genera la respuesta elegida por el atacante,
citando los documentos maliciosos como fuente
```

## Por qué es significativo para el seminario

Concretiza el riesgo de RAG Poisoning con números reales. Mientras [[inyeccion-indirecta-de-prompts|Greshake (2023)]] demuestra *que* es posible atacar vía contenido recuperado, PoisonedRAG demuestra *cuán eficiente y sigiloso* puede ser ese ataque a escala industrial.

## Relevancia para el seminario

- **Módulo 2:** Caso concreto de RAG Poisoning — complementa Greshake con resultados cuantitativos. La pregunta pedagógica clave: ¿cómo auditás una base RAG con millones de documentos cuando el ratio de envenenamiento es <0.001%?
- **Módulo 4:** El envenenamiento de la base RAG es una forma de compromiso persistente — los textos maliciosos permanecen en la base hasta que son explícitamente removidos.

Ver también: [[inyeccion-indirecta-de-prompts]], [[long-term-memory-poisoning]], [[rag-poisoning]]

## Citas destacadas

> "PoisonedRAG could achieve a 90% attack success rate when injecting five malicious texts for each target question into a knowledge database with millions of texts."

## Fuente completa

Wei Zou, Runpeng Geng, Binghui Wang, Jinyuan Jia.  
*PoisonedRAG: Knowledge Corruption Attacks to Retrieval-Augmented Generation of Large Language Models.*  
USENIX Security Symposium 2025, páginas 3827–3844.  
arXiv: https://arxiv.org/abs/2402.07867  
USENIX: https://www.usenix.org/conference/usenixsecurity25/presentation/zou-poisonedrag
