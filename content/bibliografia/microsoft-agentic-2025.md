---
title: "Ambient and Autonomous Security for the Agentic Era"
created: 2026-05-30
updated: 2026-05-30
type: paper
modulos: [3, 5]
tags: [defensa, gobernanza, agente-sprawl, observabilidad, autonomous-defense]
fuentes: ["Microsoft Security Blog, Vasu Jakkal, 18 nov 2025"]
---

## Resumen ejecutivo

Documento estratégico de Microsoft que define el paradigma de seguridad para la era agéntica: la seguridad debe ser tan autónoma y ubicua como los agentes que protege. Introduce el concepto de seguridad *ambient* — integrada en cada capa del stack, desde hardware hasta aplicaciones.

## Ideas clave

1. **Seguridad ambient:** la defensa no puede ser un perímetro externo cuando los agentes operan en todas partes. Debe estar integrada en silicio, SO, agentes, datos y plataformas simultáneamente.
2. **Tres pilares:** (a) asegurar agentes e IA, (b) proteger plataformas y nubes, (c) defender con IA agéntica.
3. **Agent sprawl como amenaza:** la proliferación de agentes no auditados ("agentes sombra") crea superficie de ataque invisible para los equipos de seguridad.
4. **Microsoft Agent 365:** plano de control para observabilidad, gestión de acceso y políticas sobre agentes heterogéneos (propios, open-source, terceros).
5. **Defensa agéntica:** Security Copilot como plataforma de SOC agéntico; agentes de triaje autónomo que identifican alertas maliciosas 6.5x más rápido que analistas humanos solos.

## Amenazas identificadas

- Prompt injection contra agentes en producción
- Exfiltración de datos sensibles vía agentes comprometidos
- Movimiento lateral post-compromiso mediante agentes con privilegios
- Agentes sombra no autorizados fuera del gobierno de IT

## Relevancia para el seminario

- **Módulo 3:** Amenazas en orquestación — agent sprawl, agentes sombra, movimiento lateral.
- **Módulo 5:** Arquitecturas defensivas — el modelo de seguridad ambient como referencia de gobernanza empresarial.

Ver también: [[gobernanza-agentica]], [[exceso-de-agencia|Least Agency]], [[mcp-tool-shadowing|Sandboxing MCP]]

## Citas destacadas

> "Security must be ambient and autonomous, like the AI it protects — woven into and around everything from silicon to operating systems, agents, apps, data, platforms, and clouds."

## Fuente completa

Vasu Jakkal, Corporate Vice President, Microsoft Security.  
*Ambient and Autonomous Security for the Agentic Era.*  
Microsoft Security Blog, 18 de noviembre de 2025.  
https://www.microsoft.com/en-us/security/blog/2025/11/18/ambient-and-autonomous-security-for-the-agentic-era/

> **Nota:** La bibliografía del seminario referencia este tema como "Defending the Agentic Edge: Enterprise Security Strategies for Autonomous LLMs". No se encontró un documento con ese título exacto. Esta entrada es la fuente de Microsoft más cercana al tema publicada en 2025.
