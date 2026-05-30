---
title: "EchoLeak (CVE-2025-32711)"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [2]
tags: [echoleak, zero-click, prompt-injection, microsoft-365, exfiltracion, caso-real]
fuentes: ["[[snailsploit-2026]]"]
---

## Resumen

EchoLeak es el primer zero-click prompt injection documentado contra un sistema en producción. CVSS 9.3. Afectó a Microsoft 365 Copilot — un email crafteado exfiltraba automáticamente datos de OneDrive, SharePoint y Teams sin ninguna interacción del usuario. Impacto estimado: $200 millones en 160+ incidentes en Q1 2025.

## Mecanismo

```
Atacante → envía email con prompt injection embebido (invisible para el usuario)
                    ↓
Microsoft 365 Copilot → procesa el email automáticamente (sin acción del usuario)
                    ↓
El prompt injection instruye al Copilot a:
  1. Acceder a archivos en OneDrive / SharePoint / Teams
  2. Exfiltrar el contenido vía parámetros de URL en tags de imagen markdown
                    ↓
Los datos viajan en el request HTTP que carga la imagen
Sin que el usuario haya hecho clic en nada
```

## Por qué es significativo

- **Zero-click:** el vector de ataque se activa solo con recibir el email — no requiere que el usuario abra, haga clic, ni apruebe nada.
- **Escala:** Microsoft 365 Copilot tiene acceso a todo el tenant del usuario — emails, archivos, calendarios, chats.
- **Técnica reutilizable:** la exfiltración vía parámetros de URL en imagen markdown es una técnica general documentada en múltiples sistemas.

## Aplicación de la Lethal Trifecta

| Componente | En EchoLeak |
|------------|-------------|
| Acceso a datos privados | ✅ OneDrive, SharePoint, Teams |
| Exposición a contenido no confiable | ✅ Emails entrantes procesados por Copilot |
| Vector de exfiltración | ✅ HTTP request al cargar imagen markdown |

Los tres presentes → ataque exitoso.

## En el contexto del seminario

- **Módulo 2:** Caso de estudio principal del módulo. Concretiza el concepto de indirect prompt injection con un incidente real de alto impacto. Útil para demostrar que estos ataques no son teóricos.

## Técnica de exfiltración relacionada: ASCII Smuggling

En ataques similares, Rehberger documentó el uso de caracteres Unicode Tags (U+E0000–U+E007F) que son visualmente invisibles pero se incluyen en URLs, permitiendo exfiltrar datos sin que aparezcan en la interfaz del usuario.

## Vectores relacionados / Defensas

- **Relacionados:** [[inyeccion-indirecta-de-prompts]], [[lethal-trifecta]], [[confused-deputy]]
- **Defensas:** sanitización de contenido de emails antes de procesamiento por LLM, restricción de acceso del Copilot a datos según contexto, bloqueo de renders de imágenes externas

## Fuentes

- [[snailsploit-2026]] — análisis del incidente
- CVE-2025-32711
