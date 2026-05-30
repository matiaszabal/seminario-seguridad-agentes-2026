---
title: "RAG Poisoning"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [2, 4]
tags: [rag, envenenamiento, base-de-conocimiento, persistencia, retrieval]
fuentes: ["[[poisonedrag-2025]]", "[[greshake-2023]]"]
---

## Resumen

Ataque que inyecta documentos maliciosos en la base de conocimiento RAG de un sistema agéntico para manipular sus respuestas. A diferencia de IPI de sesión, el compromiso es persistente: afecta a todos los usuarios que hagan preguntas relacionadas con el tema envenenado.

## Definición

Subclase de [[inyeccion-indirecta-de-prompts]] donde el vector de ataque es la base vectorial (RAG) del sistema. El atacante inyecta documentos optimizados para: (A) ser recuperados por el retriever para preguntas objetivo, y (B) inducir la respuesta falsa o maliciosa en el LLM generativo.

## Mecanismo

```
Atacante inyecta documentos maliciosos en la base RAG
                    ↓
Usuario hace una pregunta objetivo legítima
                    ↓
Retriever recupera los documentos maliciosos (optimizados para rankear alto)
                    ↓
LLM genera la respuesta elegida por el atacante,
citando los documentos maliciosos como fuente
```

## Números clave (PoisonedRAG)

Ver [[poisonedrag-2025]]:
- 5 documentos maliciosos en una base de 1.000.000 → 90% de tasa de éxito
- Ratio de envenenamiento: 0.0005% — invisible para auditoría estadística
- Efectivo en configuraciones de caja negra (el atacante no conoce el embedding model)

## Diferencia con IPI de sesión

| | IPI de sesión | RAG Poisoning |
|---|---|---|
| Persistencia | Una sesión | Hasta que el doc sea removido |
| Escala | Un usuario | Todos los usuarios que pregunten |
| Detectabilidad | Media (el doc externo es visible) | Baja (ratio 0.0005%) |

## En el contexto del seminario

- **Módulo 2:** Caso concreto de ataque persistente a la base de conocimiento de ARIA (ChromaDB). Lab M2b demuestra la técnica.
- **Módulo 4:** Contraste con LTM Poisoning — RAG Poisoning afecta a todos los usuarios, LTM Poisoning afecta la memoria personal de un usuario específico.

## Defensas

- Separación de bases RAG por trust level (docs internos vs. docs de clientes)
- Etiquetado de fuentes con metadatos de procedencia
- System prompt de desconfianza de fuentes externas como instrucciones

## Fuentes

- [[poisonedrag-2025]] — ataque formal, números concretos
- [[greshake-2023]] — contexto de IPI como clase de ataque
