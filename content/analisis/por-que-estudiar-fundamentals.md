---
title: "Por qué estudiar los fundamentos del ML (insights del libro)"
created: 2026-05-30
updated: 2026-05-30
type: analisis
modulos: [2, 4]
tags: [fundamentos, rag, clasificacion, cnn, aprendizaje]
fuentes: []
---

## Resumen

Reflexión sobre la importancia de dominar los fundamentos del ML, con dos insights concretos: RAG puede modelarse como un problema de clasificación clásico, y las CNN siguen siendo herramientas relevantes hoy en día.

---

## Pregunta disparadora

**¿Por qué es importante estudiar los fundamentos del ML?**

---

## Ideas clave

### 1. RAG puede verse como un problema de clasificación clásico

Retrieval-Augmented Generation no es solo una técnica de recuperación: en el fondo es un problema de clasificación. El sistema debe decidir qué fragmentos del corpus son relevantes para una consulta dada — exactamente lo que hace un clasificador.

**Implicación para el seminario:** entender este encuadre ayuda a analizar los vectores de ataque sobre RAG (como [[conceptos/inyeccion-indirecta-de-prompts]] y el envenenamiento descrito en [[bibliografia/poisonedrag-2025]]) desde primeros principios. Un atacante que conoce el mecanismo de scoring puede diseñar documentos maliciosos que "clasifiquen" como altamente relevantes.

### 2. Las CNN todavía son relevantes

A pesar del dominio de los Transformers, las Redes Convolucionales siguen vigentes por varios motivos (eficiencia en recursos, interpretabilidad de filtros, dominios con estructura espacial fuerte, pipelines legacy en producción).

**Implicación para el seminario:** los agentes de IA desplegados en producción con frecuencia usan modelos de percepción más antiguos (CNN, embeddings clásicos) como herramientas o conectores. Conocer sus propiedades es necesario para evaluar la superficie de ataque completa del sistema agéntico.

---

## Conexión con el seminario

| Módulo | Conexión |
|--------|----------|
| Módulo 2 | RAG como clasificación → base para entender RAG poisoning y ataques de recuperación |
| Módulo 4 | Modelos legacy en memoria/persistencia → superficie de ataque amplificada |

---

## Fuentes

- Libro (pendiente identificar referencia completa)
