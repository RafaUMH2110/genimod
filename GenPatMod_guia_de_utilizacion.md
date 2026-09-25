# GenPatMod — Guía de utilización

**GenPatMod** es una aplicación web de un solo fichero (`index.html`) que, a partir de la descripción libre de una prenda, genera con IA sus piezas de patrón de corte en SVG y las exporta como un PDF listo para imprimir en A4.

No necesita instalación ni servidor: se abre directamente en el navegador (o se aloja gratis en GitHub Pages) y funciona con tu propia clave de API de Anthropic, OpenAI o Google.

---

## 1. Requisitos

- Un navegador moderno (Chrome, Edge, Firefox o Safari).
- Una clave de API de al menos uno de estos proveedores:
  - **Anthropic (Claude):** https://console.anthropic.com/settings/keys
  - **OpenAI (GPT):** https://platform.openai.com/api-keys
  - **Google (Gemini):** https://aistudio.google.com/app/apikey
- Una impresora (opcional) para imprimir el PDF a tamaño real.

> 🔒 La clave de API se guarda **solo en tu navegador** (en `localStorage`) y se envía **únicamente** al endpoint oficial del proveedor elegido. GenPatMod no tiene servidor propio ni almacena nada en la nube.

---

## 2. Puesta en marcha

1. Descarga el fichero `index.html`.
2. Ábrelo con doble clic (se abrirá en tu navegador) o súbelo a GitHub Pages para tener una URL pública:
   1. Crea un repositorio en GitHub y sube el fichero como `index.html` en la raíz.
   2. Ve a **Settings → Pages → Build and deployment → Source: "Deploy from a branch"**, elige la rama `main` y la carpeta `/ (root)`.
   3. En un par de minutos la app estará en `https://<tu-usuario>.github.io/<tu-repositorio>/`.

---

## 3. Uso paso a paso

### Paso 1 — Configura el proveedor de IA
En el bloque **"1. Configuración del proveedor de IA"**:
- Elige **Proveedor** (Anthropic, OpenAI o Google) y **Modelo** (o "Otro…" para escribir el identificador exacto de un modelo que no esté en la lista).
- Ajusta, si quieres, el **Nº máx. tokens de salida** (por defecto 4096). Debajo del campo se indica el límite máximo admitido por el modelo elegido; si escribes un valor mayor, se recorta automáticamente a ese límite.
- Pega tu **API key** y pulsa **Guardar clave** (se recuerda para la próxima vez que abras la app). **Borrar clave** la elimina de tu navegador.

### Paso 2 — Describe la prenda
En el bloque **"2. Describe la prenda"**:
- Escribe la **idea inicial** en lenguaje natural, por ejemplo: *"una camisa clásica de manga larga con cuello camisero, puños y botonadura delantera, talla M"*. Cuanto más detalle des (tipo de prenda, talla, elementos como cuello, bolsillos, cierres…), mejor será el resultado.
- Opcionalmente, indica un **nombre de fichero** para el PDF y/o para el `.txt` de prompts; si los dejas en blanco, se generan nombres automáticos a partir del nombre de la prenda.
- La casilla **"Mostrar el prompt mejorado en pantalla"** controla si, tras planificar las piezas, se muestra en pantalla el listado de piezas que la IA ha decidido generar (activada por defecto).
- Pulsa **Generar patrón**.

### Paso 3 — Revisa el resultado
La app trabaja en tres fases, indicadas en un mensaje de estado:
1. **Planificación:** la IA decide qué piezas necesita la prenda (delantero, espalda, manga, cuello…) y sus medidas aproximadas.
2. **Generación de cada pieza:** la IA dibuja el SVG de cada pieza, una por una.
3. **Construcción del PDF:** un proceso determinista (sin IA) calcula el escalado a A4 de cada pieza, la centra, añade una regla de control y compone el PDF final.

Al terminar verás:
- El **prompt mejorado** (si la casilla del paso 2 estaba activada): el plan de piezas decidido por la IA.
- Una **tarjeta por pieza**, con su vista previa en pantalla, su descripción y un botón **"⬇️ Descargar SVG"** para guardar esa pieza por separado.
- Una **vista previa del PDF completo** y el botón **"Descargar PDF"**.
- Un contador de **tokens usados y coste estimado** de la sesión.

### Paso 4 — Editar el plan y volver a generar (opcional)
Si el plan de piezas no es el que esperabas, puedes corregirlo sin repetir todo el proceso:
1. Pulsa **"✏️ Editar prompt mejorado"**: el cuadro de texto se vuelve editable.
2. Cambia lo que necesites — el nombre o la descripción de una pieza, añade o quita piezas — manteniendo el formato: una línea `Prenda: ...` y una lista numerada (`1. Nombre`, seguido de su descripción).
3. Pulsa **"🔁 Generar con este prompt editado"**. La app generará las piezas y el PDF directamente a partir de tu texto, sin volver a llamar a la IA para planificar.

### Paso 5 — Exportar los prompts usados
El botón **"Exportar prompts (.txt)"** descarga un fichero de texto con la idea inicial, el plan de piezas y los prompts (sistema + usuario) empleados en cada llamada a la IA durante esa generación — útil para documentar o repetir el proceso.

---

## 4. El PDF generado

El PDF combina:
- **1 o 2 páginas iniciales** con el mismo contenido que se muestra en pantalla en "Prompt mejorado" (nombre de la prenda y lista de piezas con su descripción). Si el plan es muy largo, se trunca con un aviso, para no superar esas dos páginas.
- **Una página A4 por pieza**, con:
  - La pieza dibujada a su tamaño real, o reducida ("Escala 1:X") solo si no cabe entera en la página.
  - Un título con el nombre de la prenda, la pieza, la escala aplicada y las medidas reales.
  - Una **regla de control** de 10 cm reales, para comprobar con una regla física que la impresión no ha reescalado el documento (debe imprimirse **sin** la opción "ajustar a página").

---

## 5. Avisos y solución de problemas

- **"La respuesta de la IA se ha quedado corta por falta de tokens…"**: sube el valor de **"Nº máx. tokens de salida"** (dentro del límite del modelo) y vuelve a generar.
- **Error 401/403**: la API key no es válida o no tiene permisos para ese modelo.
- **Error 429**: se ha alcanzado el límite de uso de tu cuenta; espera unos segundos y reinténtalo.
- **Error 404**: el identificador de modelo no existe o no está disponible en tu cuenta; revísalo en el desplegable o en "Otro…".
- **Tiempo de espera superado (60 s) o error de red**: comprueba tu conexión y vuelve a intentarlo.
- **"No se ha podido interpretar el prompt editado"**: al editar el prompt mejorado a mano, no cambies la estructura general (línea `Prenda: ...` + lista numerada de piezas).

---

## 6. Advertencia

⚠️ La IA puede cometer errores en las medidas o en la forma de las piezas. Revisa siempre el patrón generado —y, si vas a coser, comprueba las medidas con la regla de control impresa— antes de cortar tela.

---

*GenPatMod · Rafael Puerto (r.puerto@umh.es)*
