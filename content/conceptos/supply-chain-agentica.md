---
title: "Supply Chain Agéntica"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [3]
tags: [supply-chain, mcp, marketplace, herramientas, terceros]
fuentes: ["[[mcp-security-2025]]", "[[snailsploit-2026]]", "[[owasp-llm-2025]]"]
---

## Resumen

Categoría de riesgos donde el vector de ataque son los componentes de terceros que un sistema agéntico integra: servidores MCP, herramientas de marketplace, modelos fine-tuneados, librerías de agentes. Corresponde a ASI04 del OWASP Top 10 Agéntico.

## Definición

Análogo al supply chain attack en software tradicional, pero con vectores específicos del ecosistema agéntico: las descripciones de herramientas (tool descriptions) son texto procesado por el LLM, lo que convierte cualquier componente de terceros en un posible vector de prompt injection.

## Las tres variantes de Tool Attack (ver [[mcp-tool-shadowing]])

1. **Tool Poisoning**: instrucción maliciosa en el docstring de una herramienta de terceros
2. **Trojan Tool**: efecto secundario silencioso en el código de una herramienta aprobada
3. **[[rug-pull|Rug Pull]]**: la descripción cambia después de la aprobación en una versión posterior

## Diferencia con supply chain tradicional

| | Supply chain software | Supply chain agéntica |
|---|---|---|
| Payload | Código ejecutable | Descripción de herramienta (texto) |
| Detección por tests | Test de regresión detecta comportamiento anómalo | Tests validan schema, no semántica del docstring |
| Mecanismo de compromiso | El código malicioso se ejecuta | El LLM sigue instrucciones de la descripción |

## ASI04 — OWASP Top 10 Agéntico

El OWASP Top 10 Agéntico (diciembre 2025) clasifica la supply chain agéntica como ASI04. Los casos documentados incluyen el GitHub MCP exploit donde un servidor MCP comprometido tenía acceso a repositorios privados.

## En el contexto del seminario

- **Módulo 3:** Vector central del bloque de Tool Shadowing. El ejercicio M3-ejercicio-ARIA.md usa tres herramientas candidatas para ilustrar los distintos tipos de supply chain risk.

## Defensas

- Tool Manifest: hash de descripciones en el momento de aprobación
- Política de actualización que requiere nueva revisión para cualquier cambio de hash de docstring
- Proceso de code review semántico (no solo de schema) para herramientas de terceros

## Fuentes

- [[mcp-security-2025]] — vulnerabilidades documentadas en el protocolo MCP
- [[owasp-llm-2025]] — clasificación ASI04
- [[snailsploit-2026]] — taxonomía y casos reales
