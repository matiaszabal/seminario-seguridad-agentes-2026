---
title: "ZombAIs"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [4]
tags: [c2, botnet-agentica, persistencia, lateral-movement, infraestructura]
fuentes: ["[[snailsploit-2026]]"]
---

## Resumen

Extensión del modelo botnet/C2 al mundo agéntico: múltiples agentes comprometidos (via LTM Poisoning, SpAIware, o Sleeper Agents) que reciben instrucciones coordinadas de una infraestructura de Command & Control, usando las capacidades de networking propias del agente.

## Definición

Un ZombAI es un agente comprometido que mantiene conexión con una infraestructura C2 externa y ejecuta instrucciones del atacante usando las herramientas legítimas disponibles para el agente — sin proceso externo, sin ejecutable, sin modificación del sistema operativo del host.

## Diferencia con botnets tradicionales

| | Botnet tradicional | ZombAI |
|---|---|---|
| Vector de infección | Exploit/malware en el OS | LTM Poisoning / SpAIware |
| Persistencia | Registro, cron, servicios del OS | Configuración del agente / LTM |
| Detectado por | EDR, análisis de procesos | Behavioral analytics del agente |
| Capacidades | Limitadas al OS comprometido | Limitadas a las tools del agente (CRM, APIs, email, código) |
| Remoción | Reimagen del sistema | Limpieza de memoria del agente |

## El caso documentado

Kai Aizen (Snailsploit, 2026) documentó el uso de Sliver C2 (framework de red team de código abierto) en combinación con agentes comprometidos. Los agentes hacen polling a la infraestructura C2 para recibir instrucciones actualizadas — usando llamadas HTTP normales del agente, indistinguibles de llamadas legítimas a APIs externas.

## Incidente relacionado: Claude Code Espionage Campaign (sep–nov 2025)

Actor estatal (atribuido a China) usó agentes de coding comprometidos para reconocimiento autónomo con 80–90% de la tarea completada sin intervención humana. Los agentes exfiltraban código fuente y configuración de infraestructura.

## En el contexto del seminario

- **Módulo 4:** Consecuencia operativa de LTM Poisoning y SpAIware a escala. Si múltiples agentes de alto valor son infectados vía PIP o SpAIware, el atacante puede coordinarlos como botnet.

## Relación con el Kill Chain

ZombAIs corresponden a las etapas de **Persistence** (via LTM) + **Actions on Objective** coordinadas (via C2). El lateral movement de M3 (Prompt Infection, Morris II) puede ser el vector que escala el número de ZombAIs.

## Fuentes

- [[snailsploit-2026]] — definición, Sliver C2, Claude Code Espionage Campaign
