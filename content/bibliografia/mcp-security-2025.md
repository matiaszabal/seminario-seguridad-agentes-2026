---
title: "MCP Safety Audit: LLMs with the Model Context Protocol Allow Major Security Exploits"
created: 2026-05-30
updated: 2026-05-30
type: paper
modulos: [3]
tags: [mcp, tool-shadowing, prompt-injection, supply-chain, rug-pull, protocolo]
fuentes: ["arXiv:2504.03767, abril 2025"]
---

## Resumen ejecutivo

Auditoría de seguridad del Model Context Protocol (MCP), el estándar de Anthropic para conectar LLMs con herramientas externas. El paper identifica vulnerabilidades estructurales que permiten que servidores MCP maliciosos manipulen al agente sin que el usuario lo detecte.

## Ideas clave

1. **Tool shadowing:** un servidor MCP malicioso puede redefinir el comportamiento de herramientas legítimas usando descripciones que el LLM prioriza sobre las originales.
2. **Inyección vía descripciones de herramientas:** las descripciones de tools son parte del contexto del modelo — pueden contener instrucciones que el LLM ejecuta.
3. **Rug pulls en tiempo de ejecución:** un servidor puede cambiar el comportamiento de una herramienta después de que el usuario la autorizó (la autorización fue para una versión; la ejecución ocurre con otra).
4. **Transporte STDIO sin sanitización:** el canal por defecto ejecuta cualquier comando del SO sin validación ni aislamiento.
5. **Recomendaciones:** registro de servidores MCP con firmas criptográficas, scanning automatizado de descripciones de tools, principio de mínimo privilegio en concesión de permisos.

## Relevancia para el seminario

- **Módulo 3:** Es la fuente técnica principal del módulo. Define los vectores de MCP (tool shadowing, rug pulls, contaminación de supply chain de herramientas).

Ver también: [[mcp-tool-shadowing]], [[rug-pull]], [[supply-chain-agentica]]

## Citas destacadas

> "Tool descriptions processed by the LLM are treated with the same trust level as system instructions — making them a first-class attack surface."

## Fuente completa

arXiv:2504.03767 (abril 2025).  
https://arxiv.org/abs/2504.03767

> **Nota:** La bibliografía del seminario referencia este tema como "Anthropic Research (2025) — Security Protocols for Autonomous Systems: A Model Context Protocol (MCP) Analysis". No se encontró un paper de Anthropic con ese título exacto. Esta entrada corresponde a la fuente publicada más cercana al tema. Complementar con la documentación oficial de seguridad MCP de Anthropic si está disponible.
