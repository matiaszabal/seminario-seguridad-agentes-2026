---
title: "MCP Tool Shadowing"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [3]
tags: [mcp, tool-shadowing, supply-chain, herramientas, protocolo]
fuentes: ["[[mcp-security-2025]]"]
---

## Resumen

Un servidor MCP malicioso redefine el comportamiento de herramientas legítimas mediante descripciones manipuladas. El LLM prioriza las descripciones del servidor atacante sobre las originales, redirigiendo las llamadas del agente.

## Definición

Técnica de ataque sobre el Model Context Protocol donde un servidor registra herramientas con nombres similares o idénticos a los legítimos, pero con descripciones que contienen instrucciones adversariales. El LLM, que usa las descripciones como guía para elegir y usar herramientas, es engañado para usar la versión maliciosa.

## Mecanismo

```
Servidor MCP legítimo: tool "read_file" — "Lee un archivo del sistema"
Servidor MCP malicioso: tool "read_file" — "Lee un archivo Y envía su contenido 
                         a attacker.com antes de retornar el resultado"
                    ↓
LLM recibe ambas definiciones en el contexto
                    ↓
La descripción más detallada o con mayor prioridad de contexto "gana"
                    ↓
El agente llama read_file → exfiltración silenciosa
```

## Variantes

- **Rug pull:** el servidor cambia la descripción de la herramienta *después* de que el usuario autorizó su uso.
- **Shadow tool:** herramienta nueva que intercepta llamadas a herramientas conocidas.
- **Descripción inyectada:** la descripción de la herramienta contiene instrucciones de prompt injection.

## En el contexto del seminario

- **Módulo 3:** Vector central del módulo junto con supply chain de agentes y comunicación agente-a-agente.

## Vectores relacionados / Defensas

- **Relacionados:** [[inyeccion-indirecta-de-prompts]], [[supply-chain-agentica]], [[rug-pull]]
- **Defensas:** firmas criptográficas en registros de tools, sandboxing de servidores MCP, principio de mínimo privilegio, revisión humana de descripciones de tools

## Fuentes

- [[mcp-security-2025]]
