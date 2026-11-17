# Mâze mhd.

**Mâze mhd.** es una aplicación web para la lectura distante (*distant reading*) y el análisis cuantitativo de textos en alto alemán medio (mhd.). Lee ediciones en texto plano, TEI-XML (incluidas las de heiEDITIONS) y texto anotado, y ofrece frecuencias, riqueza léxica, leyes de Zipf y Heaps, análisis de verso y rima, concordancias, colocaciones, keyness, estilometría y análisis de sentimiento con SentiMhd 2.

Es un único archivo `index.html`, sin dependencias externas. Todo el cálculo se hace en el navegador, en un Web Worker, y los textos no salen del ordenador del usuario. La interfaz está disponible en español, alemán e inglés.

### El nombre

En alto alemán medio, *mâze* (también *mâȥe*) significa «medida, proporción», y *diu mâze* es la mesura, la virtud cortesana del equilibrio y la justa medida. La aplicación mide textos y busca en ellos proporciones; el nombre une ese sentido literal con la tradición de la que proceden los textos para los que está pensada.

Escríbase siempre con circunflejo, *Mâze*: sin él, en inglés se lee *maze*, «laberinto».

## Uso

- **En línea**: `https://<usuario>.github.io/maze-mhd/`.
- **En local**: descarga `index.html` y ábrelo con el navegador. No necesita instalación ni conexión.

### Publicar en GitHub Pages

1. Crea un repositorio llamado `maze-mhd` y sube a su raíz `index.html`, `README.md` y `CITATION.cff`.
2. En **Settings → Pages → Build and deployment**, elige *Deploy from a branch*, rama `main`, carpeta `/ (root)`.
3. La aplicación quedará en `https://<usuario>.github.io/maze-mhd/`.

En `CITATION.cff`, quita el `#` de las líneas `repository-code` y `url` y pon tu usuario de GitHub.

## Configuración para el alto alemán medio

Al abrir la aplicación ya viene preparada para el mhd.:

- **Lengua fija**: palabras vacías y abreviaturas del mhd. (*unde*, *daz*, *diu*, *niht*, *ouch*, *sô*…).
- **ſ → s** activado en la tokenización.
- **Capa normalizada de TEI** (`@norm` de cada `<w>`), con la forma regularizada cuando falta.
- **SentiMhd 2** como léxico por defecto, con las negaciones del mhd. (*niht*, *nie*, *niemer*, *nieman*, *nehein*, *dehein*, *niene*, *ne*…) para invertir la polaridad si se desea.
- **Plantilla de normalización** con grafías mhd. habituales (ſ → s, ů → uo, ǒ → ou y, opcionalmente, unificación de vocales largas), que se revisa y adapta a cada edición.
- **Sin fórmulas de legibilidad**, que están pensadas para prosa moderna.

## Ayuda integrada

Cada pestaña empieza con un recuadro plegable, «¿Para qué sirve este análisis?», que resume qué mide el análisis, qué preguntas permite responder y qué precauciones conviene tener al interpretarlo (por ejemplo, el efecto de la ortografía sin normalizar, o que la puntuación de las ediciones suele ser del editor). Al abrir o cerrar uno se abren o cierran todos, y la elección se recuerda.

## Formatos de entrada

- **Texto plano** (`.txt`), con cualquier codificación habitual. Conviene conservar un verso por línea para el análisis de verso y rima.
- **TEI-XML**, también ediciones como las de heiEDITIONS:
  - Se elige la **capa de texto**: normalizada (`@norm`, por defecto), regularizada (`reg`, `corr`, `expan`) o diplomática (`orig`, `sic`, `abbr`, `am`).
  - Cada `<l>` es un verso (su `@n` se usa en las citas) y cada `<div>` una sección.
  - Los saltos `<lb break="no"/>` no cortan palabras, y por defecto se excluyen `note`, `metamark`, `fw`, `del`, las iniciales guía (`cue`) y el `teiHeader`.
  - Las entidades declaradas en un DTD externo (p. ej. `&combuml;`, `&osup;`, `&bar;`) se resuelven con una tabla MUFI integrada y el texto se compone en Unicode NFC.
  - Si los `<w>` llevan `@lemma` (y `@pos`), se usan para analizar por lema y en la pestaña Gramática.
- **Salida de etiquetadores**: la del RNNTagger (forma, categoría y lema, una palabra por línea), la del TreeTagger y los CSV que exporta WebLicht se reconocen solos, aunque el archivo termine en `.txt`. El separador y las columnas se detectan automáticamente (también por la cabecera: *token*, *lemma*, *pos*…), y la aplicación indica lo que ha detectado.
- **CoNLL-U** (`.conllu`): forma, lema y categoría; las oraciones vienen del archivo.
- **Tabulado** (`.tsv`, `.vrt`, `.csv`): también con columnas indicadas a mano; admite el formato VRT de CWB (p. ej. exportaciones de ANNIS).

Con texto anotado se puede analizar por **lema** en lugar de por forma, lo que neutraliza buena parte de la variación gráfica y flexiva.

### Lematizar con el RNNTagger

Mâze no lematiza por sí misma: aprovecha el RNNTagger de Helmut Schmid (CIS, LMU München), entrenado sobre el Referenzkorpus Mittelhochdeutsch (ReM), cuyos lemas siguen las convenciones del ReM.

1. Pasa el texto (UTF-8, un verso por línea) por el RNNTagger: en línea con WebLicht (<https://weblicht.sfs.uni-tuebingen.de/>, *SfS: To TCF Converter* + *SfS: RNN Tagger*, y en la vista de tabla *Save tokens as CSV*) o con la versión descargable (<https://www.cis.lmu.de/~schmid/tools/HistoricalGermanTools>).
2. Carga el resultado en Mâze y elige **lema** como unidad de análisis.
3. El RNNTagger escribe la e de umlaut como *è* y no marca el endurecimiento final ni la simplificación de geminadas, a diferencia del Lexer. Si quieres comparar con otro texto o léxico, unifica con reglas de normalización (p. ej. `è => e`).

Con lemas, la concordancia busca todas las formas de un lema, la pestaña Gramática muestra categorías y formas por lema, y SentiMhd 2 puede compararse por lema.

**Convención de nombres** (la de Stylo): `Autor_Titulo.txt`, p. ej. `Hartmann_Erec.txt`, `Wolfram_Parzival.txt`. Lo que va antes del primer guion bajo es la clase (autor). Los archivos que empiezan por `anon_` o `desconocido_` se tratan como de autoría desconocida y la aplicación propone su atribución.

## Qué calcula

**Por texto**
- Recuentos y longitudes de palabra, oración y verso.
- Unas 30 medidas de riqueza léxica: TTR, Guiraud, Herdan, Maas, Brunet, Honoré, Sichel, Yule K/I, Simpson, MATTR, MSTTR, MTLD, HD-D, entropía, etc. Incluyen intervalos de confianza por bootstrap de bloques y un gráfico de estabilidad frente a la longitud.
- Zipf (mínimos cuadrados y Zipf-Mandelbrot), ley de potencia por máxima verosimilitud (Clauset, Shalizi y Newman 2009), espectro de frecuencias y ley de abreviación.
- Heaps con comparación frente al texto barajado; rarefacción exacta y extrapolación del vocabulario con el estimador de Chao (Chao et al. 2014); Chao1.
- **Estructura**: medidas por segmento (partes iguales, expresión regular, divisiones TEI o párrafos), densidad de hápax por segmento, palabras y terminaciones de rima, palabras iniciales de verso y ley de Menzerath-Altmann.
- Frecuencias con dispersión D de Juilland, tendencias y gráfico de dispersión, concordancias KWIC con referencia de verso y ordenación por dos claves.
- Colocados (MI, t-score, logDice, G²), red de coapariciones, n-gramas para localizar fórmulas, y nube de palabras.
- **Gramática** (con texto anotado): distribución de categorías, densidad léxica y formas por lema.

**Corpus**
- **Comparación**: tabla de medidas con rarefacción a un tamaño común, intervalos de confianza y curvas de Zipf, Heaps y rarefacción superpuestas.
- **Keyness**: log-likelihood (G²), BIC, %DIFF y log ratio frente a otro texto o al resto del corpus; útil también para comparar testimonios o redacciones.
- **Estilometría**: Delta de Burrows, de Eder, del coseno y cuadrática sobre las MFW, con culling y muestreo. Ofrece dendrograma (Ward, UPGMA, completo, simple), PCA con cargas, matriz de distancias, clasificación con validación cruzada (vecino más próximo y centroide) y atribución de textos anónimos.
- **Rolling Delta**: distancia de una ventana móvil del texto objetivo a cada autor de referencia, para detectar cambios de mano, de fuente o interpolaciones. En la transmisión manuscrita mhd. la señal mezcla autor y copista: conviene trabajar con texto normalizado o por lema.

**Léxicos y sentimiento**: densidad por categoría, índice de polaridad, arco a lo largo del texto (por segmentos o con ventana móvil), palabras que más contribuyen y comparación entre textos; opcionalmente, comparación por lema e inversión de la polaridad tras una negación. SentiMhd 2 viene con una lista inicial de palabras desactivadas (*wol*, *meinen*, *gewan*, *gerne*, *recht*, *sere*, *l*, *n*, *x*, *xi*), que actúan sobre todo como adverbios, intensificadores u homógrafos; se puede revisar o restablecer, y la elección se guarda. También se puede cargar cualquier otro léxico en TSV (categoría + formas) o una lista de palabras, en UTF-8 o Windows-1252.

**Normalización ortográfica**: reglas `origen => destino` (también con expresiones regulares). Las reglas y opciones se pueden guardar como perfiles reutilizables, que también se exportan e importan en JSON.

**Exportación**: cada tabla en CSV o Excel (.xlsx); cada gráfico en SVG o PNG; todas las métricas en JSON o Excel; y un informe HTML con parámetros, tablas y gráficos, que también puede imprimirse como PDF.

## Recursos incluidos

- **SentiMhd 2**: Dimpel, Friedrich Michael (2025): *SentiMhd 2*. DARIAH-DE. https://doi.org/10.20375/0000-0016-DADD-A. Licencia CC BY 4.0. Se incluye dentro de `index.html` sin cambios en su contenido (solo se han unificado los saltos de línea). Véase también Dimpel, F. M. (2024): «Entwicklung eines mittelhochdeutschen Sentiment-Wörterbuchs aus korpushermeneutischer Perspektive», *Zeitschrift für Literaturwissenschaft und Linguistik*, https://doi.org/10.1007/s41244-024-00339-0.

Si publicas resultados obtenidos con SentiMhd 2, cita también el léxico.

## Cómo citar

El archivo `CITATION.cff` permite que GitHub muestre el botón *Cite this repository*. Para obtener un DOI permanente:

1. Conecta la cuenta de GitHub en <https://zenodo.org> y activa el repositorio en *GitHub → Repositories*.
2. Crea una *release* en GitHub (p. ej. `v2.0.0`): Zenodo la archiva y le asigna un DOI.
3. Añade el DOI a `CITATION.cff` (campo `doi:`) y a este README.

Antes de publicar conviene elegir una licencia (p. ej. MIT o EUPL-1.2) y añadirla como `LICENSE` y en el campo `license:` de `CITATION.cff`.
