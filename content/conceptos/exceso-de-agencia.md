---
title: "Exceso de Agencia (Excessive Agency)"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [1, 5]
tags: [exceso-de-agencia, least-agency, permisos, autonomia, owasp, gobernanza]
fuentes: ["[[owasp-llm-2025]]"]
---

## Resumen

Un agente tiene exceso de agencia cuando puede hacer más de lo necesario para su tarea: accede a herramientas fuera de su scope, opera con privilegios más amplios de lo requerido, o toma acciones de alto impacto sin supervisión humana.

## Definición

LLM06 del OWASP Top 10 for LLM Applications 2025. El exceso de agencia es la condición que amplifica el impacto de cualquier otro ataque — si un agente comprometido tiene permisos mínimos, el daño está acotado; si tiene permisos excesivos, el mismo ataque puede ser catastrófico.

## Tres dimensiones (OWASP)

1. **Funcionalidad excesiva:** el agente tiene acceso a herramientas que no necesita para su función declarada.  
   *Ejemplo: un agente de atención al cliente con acceso a `delete_user()` en la base de datos.*

2. **Permisos excesivos:** las herramientas que usa operan con privilegios más amplios de lo necesario.  
   *Ejemplo: el agente usa una conexión a DB con permisos de escritura cuando solo necesita lectura.*

3. **Autonomía excesiva:** el agente ejecuta acciones de alto impacto (enviar emails, hacer pagos, borrar archivos) sin punto de aprobación humana.  
   *Ejemplo: agente de compras que puede autorizar transacciones sin confirmación del usuario.*

## En el contexto del seminario

- **Módulo 1:** El exceso de agencia es el principio que conecta la superficie de ataque con el impacto de los ataques. Un agente mínimamente privilegiado limita el blast radius de cualquier compromiso.
- **Módulo 5:** El principio de **Least Agency** es el contrapeso defensivo. Toda arquitectura defensiva parte de minimizar la agencia concedida.

## Vectores relacionados / Defensas

- **Relacionados:** [[confused-deputy]], [[inyeccion-indirecta-de-prompts]], [[mcp-tool-shadowing]]
- **Defensa principal:** principio de **Least Agency** — conceder solo las herramientas, permisos y autonomía estrictamente necesarios para cada tarea, no para cada agente

## Fuentes

- [[owasp-llm-2025]] — LLM06
