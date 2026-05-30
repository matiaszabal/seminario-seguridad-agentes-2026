---
title: "CaMeL — Defeating Prompt Injections by Design"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [5]
tags: [camel, defensa, prompt-injection, dual-llm, google-deepmind, arquitectura-defensiva]
fuentes: ["[[snailsploit-2026]]", "Google DeepMind, abril 2025"]
---

## Resumen

CaMeL (Capabilities and Memory for Language models) es la defensa más prometedora documentada contra prompt injection. Desarrollada por Google DeepMind (abril 2025), neutraliza el 67% de los ataques y resuelve el 77% de las tareas con seguridad demostrable. Es la primera mitigación creíble que no depende de agregar más IA al problema.

## Mecanismo

CaMeL extiende el patrón dual-LLM con una restricción estructural:

```
LLM Privilegiado (trusted)
  ↓ genera código en Python DSL restringido
  ↓ especifica: qué herramientas llamar y qué flujos de datos son permitidos
  ↓ NO procesa datos externos directamente

Python DSL Restringido
  ↓ ejecuta las llamadas a herramientas
  ↓ controla qué datos pueden fluir hacia qué destinos
  ↓ el LLM no puede "ver" el contenido de los datos, solo sus etiquetas

LLM Observador (untrusted context)
  ↓ procesa el contenido externo (emails, documentos, web)
  ↓ opera sin privilegios — no puede invocar herramientas directamente
```

La separación clave: **el LLM que procesa contenido no confiable no tiene acceso a herramientas**. El LLM que tiene acceso a herramientas no procesa contenido no confiable.

## Resultados

- **67% de ataques neutralizados** en el benchmark AgentDojo
- **77% de tareas completadas con seguridad demostrable** (el resto requieren human-in-the-loop)
- Evaluado específicamente contra indirect prompt injection

## Por qué Simon Willison lo destacó

> "The first credible prompt injection mitigation that doesn't just throw more AI at the problem."

La mayoría de las defensas existentes usan un LLM adicional para detectar si el contenido contiene ataques — lo que crea una carrera armamentista entre el LLM de defensa y el atacante. CaMeL evita esta carrera mediante separación arquitectural.

## Limitaciones

- No elimina todos los ataques (33% restante)
- Las tareas que requieren procesamiento de contenido externo con alta autonomía siguen siendo difíciles
- La restricción del DSL puede limitar la funcionalidad del agente

## En el contexto del seminario

- **Módulo 5:** Caso central de arquitectura defensiva. Ilustra el principio de separación de privilegios aplicado a LLMs y el valor de soluciones arquitecturales vs. filtros.

## Vectores relacionados / Defensas relacionadas

- **Complementa:** Spotlighting (Microsoft), Instruction Hierarchy (OpenAI)
- **Concepto base:** separación de contextos confiable/no confiable, dual-LLM pattern
- **Relacionados:** [[exceso-de-agencia]], [[inyeccion-indirecta-de-prompts]]

## Fuentes

- Google DeepMind (abril 2025) — paper original "Defeating Prompt Injections by Design"
- [[snailsploit-2026]] — análisis y contextualización
