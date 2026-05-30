# Guía de Setup para los Labs del Seminario

> **Tiempo estimado**: 15–20 minutos  
> **Prerrequisito**: cuenta de Google (Gmail). No se requiere instalar nada localmente.  
> **Soporte**: si encontrás algún problema en este proceso, escribir al instructor antes de la primera clase.

---

## Resumen

Los labs del seminario corren en **Google Colab** — el entorno de Python en la nube de Google, sin instalación local. Para acceder al modelo de IA (Gemini 2.0 Flash), necesitás una **API key de Google AI Studio**, que es gratuita con el tier Free.

El único requisito real es una cuenta de Google.

---

## Paso 1 — Crear la API key en Google AI Studio

### 1a. Ir a Google AI Studio

Abrí `aistudio.google.com` en tu navegador. Si ya tenés cuenta de Google, iniciá sesión. Si no, creá una cuenta de Gmail primero.

### 1b. Crear la API key

1. En el panel izquierdo, hacé clic en **"Get API key"**
2. Hacé clic en **"Create API key"**
3. Seleccioná **"Create API key in new project"** (o un proyecto existente si tenés uno)
4. Google genera una key del formato `AIzaSy...` — copiala

### 1c. Guardar la key en un lugar seguro

La key se muestra una sola vez en el momento de creación. Guardala en un lugar seguro (un gestor de contraseñas, o un archivo de texto temporal en tu computadora que no subas a ningún repositorio).

> **Importante**: nunca compartas tu API key, no la subas a GitHub, y no la pongas en el código de los notebooks. Los labs están diseñados para leerla desde los Colab Secrets (ver Paso 3).

### 1d. Verificar el tier Free

Google AI Studio tiene un tier Free que incluye:
- **Gemini 2.0 Flash**: 1500 requests por día, 15 requests por minuto
- Sin necesidad de tarjeta de crédito para el tier Free

Para los labs del seminario, el tier Free es suficiente. Si hacés muchos experimentos en un día y llegás al límite, esperá al día siguiente o al día siguiente.

---

## Paso 2 — Configurar Google Colab

### 2a. Verificar que Colab funciona

Abrí `colab.research.google.com`. Iniciá sesión con tu cuenta de Google. Si ves la pantalla de bienvenida con la opción de crear un nuevo notebook, el entorno está listo.

### 2b. Colab y tu Google Drive

Colab guarda los notebooks en tu Google Drive. Si querés mantener tus versiones de los labs con tus respuestas, asegurate de tener espacio en Drive (la cuenta gratuita tiene 15 GB, más que suficiente).

---

## Paso 3 — Configurar la API key como Colab Secret

Los labs leen la API key desde **Colab Secrets** — el sistema de variables seguras de Colab que no quedan guardadas en el código del notebook.

### 3a. Abrir el panel de Secrets

1. Abrí cualquier notebook en Colab
2. En el panel izquierdo, hacé clic en el ícono de llave (🔑) — **"Secrets"**
3. Si no ves el ícono, abrilo desde el menú **Tools → Secrets**

### 3b. Agregar la API key

1. Hacé clic en **"Add new secret"**
2. **Name**: escribí exactamente `GOOGLE_API_KEY` (sensible a mayúsculas)
3. **Value**: pegá tu API key (`AIzaSy...`)
4. Activá el toggle **"Notebook access"** para que los notebooks puedan leerla

> El secreto queda guardado en tu cuenta de Google — no en el notebook. Cuando abras cualquier notebook en Colab con tu cuenta, va a tener acceso a este secreto.

### 3c. Verificar que funciona

En cualquier celda de Colab, ejecutá:

```python
from google.colab import userdata
api_key = userdata.get("GOOGLE_API_KEY")
print(f"Key configurada: {api_key[:8]}...{api_key[-4:]}")
```

Si ves algo como `Key configurada: AIzaSyAB...XYZ4`, está correctamente configurado.

---

## Paso 4 — Test completo con el modelo

Antes de la primera clase, verificá que el modelo responde correctamente:

```python
# Celda de verificación — ejecutar antes de cada lab
import os
from google.colab import userdata

os.environ["GOOGLE_API_KEY"] = userdata.get("GOOGLE_API_KEY")

import google.generativeai as genai
genai.configure(api_key=os.environ["GOOGLE_API_KEY"])

model = genai.GenerativeModel("gemini-2.0-flash")
response = model.generate_content("Respondé solo 'OK' para confirmar que funcionás.")
print(f"✅ Gemini 2.0 Flash conectado: {response.text.strip()}")
```

Si ves `✅ Gemini 2.0 Flash conectado: OK`, todo está listo.

---

## Paso 5 — Acceder a los notebooks del seminario

Los notebooks de los labs estarán disponibles en el campus del curso. Hay dos formas de abrirlos en Colab:

### Opción A — Desde el campus (recomendada)

1. Ir al campus del curso
2. Módulo correspondiente → sección "Labs"
3. Clic en el botón "Abrir en Colab"
4. El notebook se abre directamente en tu Colab

### Opción B — Desde Google Drive

1. Descargar el `.ipynb` desde el campus
2. Subir a tu Google Drive
3. Doble clic en el archivo en Drive → se abre en Colab

### Guardar tu copia

Para guardar tus respuestas y resultados:
- **File → Save a copy in Drive** — crea una copia tuya que podés editar libremente
- Tus cambios no afectan el notebook original del campus

---

## Estructura de los labs

Los labs del seminario tienen esta estructura estándar:

```
Sección Setup
  ↓ instalación de dependencias (solo la primera vez por sesión)
  ↓ configuración de API key
  ↓ celda de verificación

Sección N — [nombre del ejercicio]
  ↓ explicación en Markdown
  ↓ código que podés ejecutar
  ↓ celda de preguntas (Markdown editable — escribí tus respuestas acá)
```

**Tiempo estimado por lab**: 45–60 minutos para completar todos los ejercicios.

---

## Preguntas frecuentes

### "La celda de verificación da error de API key"

Verificar que:
1. El nombre del secreto en Colab Secrets es exactamente `GOOGLE_API_KEY` (sin espacios, sin comillas)
2. El toggle "Notebook access" está activado para ese secreto
3. Copiaste la key completa (empieza con `AIzaSy` y tiene ~39 caracteres)

### "Gemini responde pero con error de quota"

El tier Free tiene 15 requests por minuto. Si ejecutás muchas celdas seguidas, puede que alcances el límite. Esperá 60 segundos y volvé a intentar.

### "La instalación de `google-adk` falla"

La celda de instalación hace `!pip install google-adk>=1.0.0 -q`. Si falla:
1. Verificar que la celda tiene el `!` al inicio (no es código Python, es un comando de shell)
2. Ir a **Runtime → Factory reset runtime** y volver a intentar
3. Si sigue fallando, escribir al instructor con el mensaje de error completo

### "El notebook se desconecta después de un rato"

Colab desconecta la sesión después de 90 minutos de inactividad. Si esto pasa:
1. **Runtime → Reconnect** (o hacer clic en el botón "Reconnect" que aparece)
2. Volver a ejecutar la celda de Setup desde el principio (reinstalación de dependencias + configuración de API key)
3. Las celdas de código que hayas ejecutado antes tendrán que re-ejecutarse — pero tus respuestas escritas en Markdown se preservan en el archivo guardado en Drive

### "Necesito usar Gemini 2.0 Flash pero no tengo cuenta de Google"

Crear una cuenta de Google es gratuito y es el único requisito del curso. No se puede usar otro proveedor de modelos porque el código de los labs está escrito específicamente para el Google ADK con Gemini.

---

## Límites del tier Free de Google AI Studio

| Recurso | Límite Free |
|---|---|
| Requests por día (Gemini 2.0 Flash) | 1.500 |
| Requests por minuto | 15 |
| Tokens por minuto | 1.000.000 |
| Tarjeta de crédito requerida | No |

Para los labs del seminario, estos límites son ampliamente suficientes. Si un lab completo usa ~50 requests, con 1.500 por día podés hacer 30 ejecuciones completas del lab por día.

---

## Soporte técnico antes de la primera clase

Si el setup no funciona después de seguir esta guía, enviá un email al instructor con:
1. En qué paso te trabaste
2. El mensaje de error exacto (captura de pantalla o texto copiado)
3. Qué sistema operativo y navegador estás usando

El setup puede resolverse en la mayoría de los casos en 5–10 minutos con el mensaje de error correcto.
