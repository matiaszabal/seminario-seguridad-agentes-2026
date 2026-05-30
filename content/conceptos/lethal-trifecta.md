---
title: "Lethal Trifecta (Trifecta de Willison)"
created: 2026-05-30
updated: 2026-05-30
type: concepto
modulos: [1, 2]
tags: [lethal-trifecta, willison, explotabilidad, diagnostico, framework]
fuentes: ["[[snailsploit-2026]]", "Simon Willison"]
---

## Resumen

Framework diagnóstico de Simon Willison: un sistema agéntico es explotable cuando combina tres capacidades simultáneamente — acceso a datos privados, exposición a contenido no confiable, y un vector de exfiltración. La ausencia de cualquiera de los tres rompe la cadena de ataque.

## Definición

La **Lethal Trifecta** (trifecta letal) es la condición necesaria y suficiente para que un ataque de prompt injection sea peligroso en la práctica. Willison la formuló como herramienta de threat assessment para agentes.

## Los tres componentes

```
        [1] Acceso a datos privados
               ↓
    El agente puede leer: emails, documentos, 
    credenciales, datos de otros usuarios
    
        [2] Exposición a contenido no confiable
               ↓
    El agente procesa: páginas web, documentos 
    externos, emails entrantes, resultados de búsqueda
    
        [3] Vector de exfiltración
               ↓
    El agente puede: hacer HTTP requests, enviar emails,
    escribir en sistemas externos, llamar APIs
```

**Si los tres están presentes → el sistema es explotable.**  
Un atacante que controla el contenido en [2] puede usar [1] para robar datos y [3] para extraerlos.

## Uso pedagógico

La trifecta es una herramienta de evaluación rápida. Ante cualquier sistema agéntico, preguntar:

| Capacidad | ¿Presente? | Mitigación |
|-----------|-----------|------------|
| ¿Accede a datos privados del usuario? | ✅/❌ | Reducir scope de datos accesibles |
| ¿Procesa contenido de fuentes externas? | ✅/❌ | Sanitizar / desconfiar por defecto |
| ¿Puede hacer acciones externas (HTTP, email)? | ✅/❌ | Human-in-the-loop para acciones salientes |

Si las tres son ✅, el sistema tiene alta exposición a indirect prompt injection.

## Cita original

> "The moment you give them access to tools, the stakes in terms of prompt injection goes sky high." — Simon Willison

## En el contexto del seminario

- **Módulo 1:** Marco de evaluación de la superficie de ataque. Permite que los alumnos analicen cualquier sistema agéntico y determinen su nivel de exposición.
- **Módulo 2:** Aparece como diagnóstico previo a cada caso de estudio (EchoLeak, RAG poisoning, email injection).

## Vectores relacionados / Defensas

- **Relacionados:** [[confused-deputy]], [[inyeccion-indirecta-de-prompts]], [[exceso-de-agencia]]
- **Defensas:** atacar cada componente de la trifecta — reducir scope de datos, sanitizar contenido externo, requerir aprobación humana para acciones salientes

## Fuentes

- Simon Willison — formulación original
- [[snailsploit-2026]] — aplicación al threat landscape agéntico
