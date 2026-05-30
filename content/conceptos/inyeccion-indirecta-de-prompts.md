---
title: "Inyección Indirecta de Prompts"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [1, 2]
tags: [prompt-injection, ataque, inyeccion-indirecta, retrieval, rag]
fuentes: ["[[greshake-2023]]"]
---

## Resumen

El atacante inyecta instrucciones maliciosas en datos externos (páginas web, documentos, emails) que el agente va a recuperar y procesar. El agente ejecuta las instrucciones sin que el usuario las vea o las autorice.

## Definición

Ataque en el que el payload no viene del usuario sino del **entorno** que el agente consulta. La distinción clave con prompt injection directa: el atacante no tiene acceso a la interfaz del sistema — solo necesita controlar algún dato que el agente pueda leer.

## Mecanismo

```
Atacante → inyecta instrucción en [web / email / doc / DB]
                    ↓
Agente → recupera contenido (RAG, scraping, lectura de email)
                    ↓
LLM → procesa el contenido con el mismo nivel de confianza
      que las instrucciones del sistema
                    ↓
Agente → ejecuta la instrucción maliciosa
```

El agente confunde **datos** con **instrucciones** porque ambos llegan como texto en el contexto.

## En el contexto del seminario

- **Módulo 1:** Introduce por qué el acceso a herramientas y contexto externo amplía la superficie de ataque más allá del prompt del usuario.
- **Módulo 2:** El módulo entero gira en torno a este vector — variantes en RAG, email, scraping, y el caso EchoLeak.

## Vectores relacionados / Defensas

- **Relacionados:** [[rag-poisoning]], [[confused-deputy]], [[mcp-tool-shadowing]]
- **Defensas:** sanitización de contenido recuperado, separación de canales de datos e instrucciones (CaMeL), desconfianza por defecto de contenido externo

## Fuentes

- [[greshake-2023]] — paper fundacional
