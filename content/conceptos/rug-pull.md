---
title: "Rug Pull"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [3]
tags: [mcp, supply-chain, post-aprobacion, herramientas, versionado]
fuentes: ["[[mcp-security-2025]]", "[[snailsploit-2026]]"]
---

## Resumen

Ataque de supply chain donde un proveedor de herramientas modifica la descripción (docstring) de una herramienta **después** de que fue aprobada por el equipo de seguridad. La nueva descripción contiene instrucciones maliciosas que el LLM seguirá en ejecuciones futuras, sin que el sistema de aprobación lo detecte.

## Mecanismo

```
T=0:  El equipo de seguridad revisa y aprueba herramienta v1.0.0
      La descripción está limpia — el LLM la usa correctamente

T+N semanas: El proveedor publica v1.0.1 como "bug fix"
             El pipeline de CI/CD actualiza automáticamente
             La descripción cambió una línea — instrucción maliciosa agregada
             Los tests de integración pasan (la función retorna el mismo schema)
             Nadie revisó porque era un "patch menor"
```

## Por qué el período de latencia es deliberado

El proveedor espera a establecer confianza antes de activar el payload. El período de latencia cumple dos funciones: (1) el proveedor se establece como legítimo — los patches rutinarios dejan de generar revisión, (2) se establece el precedente de que los "bug fix patches" son aprobados sin revisión de seguridad.

## Diferencia con Tool Poisoning

| | Tool Poisoning | Rug Pull |
|---|---|---|
| Cuándo se activa | Desde el primer deploy | Después de N días/semanas post-aprobación |
| Detectable en el deploy inicial | ✅ Si se lee el docstring | ✅ v1.0.0 está limpio |
| Detectable en el update | N/A | Solo con Tool Manifest que alerta en cambios de hash |

## Defensa: Tool Manifest

El [[mcp-tool-shadowing|Tool Manifest]] registra el hash SHA256 del docstring al momento de aprobación. Cualquier cambio en el docstring en versiones futuras genera una alerta antes del deploy. Ver lab M3a — Ejercicio 4.

## En el contexto del seminario

- **Módulo 3:** Variante del Tool Shadowing. Parte del lab M3a (Ejercicio 4). El diff v1.0.0→v1.0.1 es el artefacto clave del ejercicio.

## Fuentes

- [[mcp-security-2025]] — contexto de vulnerabilidades MCP
- [[snailsploit-2026]] — taxonomía de Tool Attacks
