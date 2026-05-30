---
title: "Not What You've Signed Up For: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection"
created: 2026-05-30
updated: 2026-05-30
type: paper
modulos: [1, 2]
tags: [prompt-injection, inyeccion-indirecta, rag-poisoning, data-theft, worming]
fuentes: ["Greshake et al., arXiv:2302.12173, AISec '23"]
---

## Resumen ejecutivo

Paper fundacional del campo. Introduce el concepto de **inyección indirecta de prompts**: el atacante no interactúa directamente con el LLM sino que inyecta instrucciones en datos que el modelo va a recuperar (web, email, documentos). La línea entre datos e instrucciones se borra cuando el modelo tiene acceso a contenido externo.

## Ideas clave

1. **El vector es el contenido recuperado, no el usuario.** El atacante inyecta prompts en páginas web, emails o documentos que el agente va a leer. El usuario nunca ve la instrucción maliciosa.
2. **Demostración contra sistemas reales.** Los ataques funcionaron contra Bing Chat (GPT-4), motores de autocompletado de código, y agentes sintéticos sobre GPT-4.
3. **Taxonomía de impacto:** robo de datos, *worming* (propagación del ataque a través de conversaciones de otros usuarios), contaminación del ecosistema informativo, llamadas no autorizadas a APIs.
4. **Los prompts recuperados actúan como ejecución arbitraria.** El modelo los procesa con el mismo nivel de confianza que las instrucciones del sistema.
5. **Las defensas en 2023 eran insuficientes.** El paper estableció la urgencia del problema antes de que existieran mitigaciones robustas.

## Relevancia para el seminario

- **Módulo 1:** Ilustra por qué la arquitectura de recuperación de contexto amplía la superficie de ataque.
- **Módulo 2:** Es el paper de referencia del módulo. Define la clase de ataque, la taxonomía y los ejemplos reales. Todo el módulo construye sobre este trabajo.

Ver también: [[inyeccion-indirecta-de-prompts]], [[rag-poisoning]], [[confused-deputy]]

## Citas destacadas

> "LLM-Integrated Applications blur the line between data and instructions."

> "Processing retrieved prompts can act as arbitrary code execution, manipulate the application's functionality, and control how and if other APIs are called."

## Fuente completa

Kai Greshake, Sahar Abdelnabi, Shailesh Mishra, Christoph Endres, Thorsten Holz, Mario Fritz.  
*Not What You've Signed Up For: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection.*  
arXiv:2302.12173 (2023). Presentado en AISec '23 (ACM CCS Workshop).  
https://arxiv.org/abs/2302.12173

> **Nota:** El seminario referencia este trabajo como "(2024)" pero el preprint es de febrero 2023 y la versión de conferencia es de noviembre 2023.
