# Mâze mhd.

**Mâze mhd.** es una aplicación web para la lectura distante (*distant reading*) y el análisis cuantitativo de textos en alto alemán medio (mhd.), en verso o en prosa, con lematización integrada a partir del ReM. Lee ediciones en texto plano, TEI-XML (incluidas las de heiEDITIONS) y texto anotado, y ofrece frecuencias, riqueza léxica, leyes de Zipf y Heaps, análisis de verso y rima, concordancias, colocaciones, keyness, estilometría y análisis de sentimiento con SentiMhd 2.

Es un único archivo `index.html`, sin dependencias externas. Todo el cálculo se hace en el navegador, en un Web Worker, y los textos no salen del ordenador del usuario. La interfaz está disponible en español, alemán e inglés.

### El nombre

En alto alemán medio, *mâze* (también *mâȥe*) significa «medida, proporción», y *diu mâze* es la mesura, la virtud cortesana del equilibrio y la justa medida. La aplicación mide textos y busca en ellos proporciones; el nombre une ese sentido literal con la tradición de la que proceden los textos para los que está pensada.

## Uso

- **En línea**: `https://javiermunoz-acebes.github.io/maze-mhd/`.
- **En local**: descarga `index.html` y ábrelo con el navegador. No necesita instalación ni conexión.

## Configuración para el alto alemán medio

Al abrir la aplicación ya viene preparada para el mhd.:

- **Lengua fija**: palabras vacías y abreviaturas del mhd. (*unde*, *daz*, *diu*, *niht*, *ouch*, *sô*…).
- **ſ → s** activado en la tokenización.
- **TEI**: capa regularizada por defecto y **rimas normalizadas** con el `@norm` de los `<w>`. En heiEDITIONS, `@norm` solo aparece en la palabra en rima (la forma normalizada que permite estudiar la rima), por lo que Mâze lo usa para el análisis de rimas y para lematizar esas palabras, no como capa del texto entero.
- **SentiMhd 2** como léxico por defecto, con las negaciones del mhd. (*niht*, *nie*, *niemer*, *nieman*, *nehein*, *dehein*, *niene*, *ne*…) para invertir la polaridad si se desea.
- **Plantilla de normalización** con grafías mhd. habituales (ſ → s, ů → uo, ǒ → ou y, opcionalmente, unificación de vocales largas), que se revisa y adapta a cada edición.
- **Sin fórmulas de legibilidad**, que están pensadas para prosa moderna.

## Ayuda integrada

Cada pestaña empieza con un recuadro plegable, «¿Para qué sirve este análisis?», que resume qué mide el análisis, qué preguntas permite responder y qué precauciones conviene tener al interpretarlo (por ejemplo, el efecto de la ortografía sin normalizar, o que la puntuación de las ediciones suele ser del editor). Al abrir o cerrar uno se abren o cierran todos, y la elección se recuerda.

## Verso y prosa

La opción **Tipo de texto** decide cómo se leen los saltos de línea. Por defecto se detecta automáticamente (en TEI, por la presencia de `<l>`; en texto plano, por la longitud de las líneas y los guiones a final de línea), y el resumen indica qué se ha detectado; se puede fijar a mano.

- **Verso**: cada línea es un verso (en TEI, cada `<l>`; sin `<l>`, cada `<lb/>`). Se analizan rima, inicio de verso, longitud de verso y la ley de Menzerath por verso, y las citas se dan por verso.
- **Prosa**: los saltos de línea simples se leen como espacios y se unen las palabras partidas a final de línea (`-`, `¬`, `⸗`); en TEI, `<lb/>` es un espacio. El texto se organiza en párrafos (líneas en blanco o `<p>`) y oraciones, las citas se dan por párrafo u oración, y no se muestran los análisis de verso.

## Formatos de entrada

- **Texto plano** (`.txt`), con cualquier codificación habitual. Conviene conservar un verso por línea para el análisis de verso y rima.
- **TEI-XML**, también ediciones como las de heiEDITIONS:
  - Se elige la **capa de texto**: regularizada (`reg`, `corr`, `expan`, por defecto), diplomática (`orig`, `sic`, `abbr`, `am`) o «regularizada + `@norm`», que sustituye cada `<w>` con `@norm` por esa forma. Como en heiEDITIONS `@norm` marca solo la palabra en rima, esta última opción no da un texto normalizado; las rimas se toman de `@norm` en cualquier capa.
  - Cada `<l>` es un verso (su `@n` se usa en las citas) y cada `<div>` una sección.
  - Los saltos `<lb break="no"/>` no cortan palabras, y por defecto se excluyen `note`, `metamark`, `fw`, `del`, las iniciales guía (`cue`) y el `teiHeader`.
  - Con la opción **dividir en textos por divisiones**, cada `<div>` de primer nivel se analiza como un texto aparte, llamado `n_archivo` (p. ej. `M_Erek_A_Wien` y `E_Erek_A_Wien`): sirve para comparar partes de un mismo códice o edición.
  - Las entidades declaradas en un DTD externo (p. ej. `&combuml;`, `&osup;`, `&bar;`) se resuelven con una tabla MUFI integrada y el texto se compone en Unicode NFC.
  - Si los `<w>` llevan `@lemma` (y `@pos`), se usan para analizar por lema y en la pestaña Gramática.
- **Salida de etiquetadores**: la del RNNTagger (forma, categoría y lema, una palabra por línea), la del TreeTagger y los CSV que exporta WebLicht se reconocen solos, aunque el archivo termine en `.txt`. El separador y las columnas se detectan automáticamente (también por la cabecera: *token*, *lemma*, *pos*…), y la aplicación indica lo que ha detectado.
- **CoNLL-U** (`.conllu`): forma, lema y categoría; las oraciones vienen del archivo.
- **Tabulado** (`.tsv`, `.vrt`, `.csv`): también con columnas indicadas a mano; admite el formato VRT de CWB (p. ej. exportaciones de ANNIS).

Con texto anotado se puede analizar por **lema** en lugar de por forma, lo que neutraliza buena parte de la variación gráfica y flexiva.

### Lematización integrada (ReM)

Mâze mhd. lematiza por sí misma los textos que no traen lemas. Usa una tabla de formas y lemas sacada del **Referenzkorpus Mittelhochdeutsch** (ReM 2.1: unos 2,1 millones de tokens anotados a mano), que va comprimida dentro de `index.html` (unos 1,7 MB).

- A cada palabra se le asigna el lema y la categoría (HiTS) más frecuentes de esa forma en el ReM, buscando la forma normalizada y la diplomática, con y sin signos de longitud. **No tiene en cuenta el contexto**: los homógrafos (*daz* conjunción o artículo, *ir* pronombre o posesivo) reciben siempre la lectura más frecuente.
- Los lemas siguen las convenciones del ReM y del *Mittelhochdeutsches Wörterbuch*. Por defecto se muestran simplificados (sin *ë*/*è*, guiones ni partes opcionales: *sprëchen* → *sprechen*, *ge-winnen* → *gewinnen*, *sæl(e)de* → *sælde*); la opción se puede desactivar.
- **Precisión**: probada en 25 textos del ReM que no se usaron para construir la tabla, encuentra el 97,7 % de las formas normalizadas y acierta el lema en el 94,7 % de ellas (la categoría, en el 85,8 %). En el Erek A del Ambraser Heldenbuch, con grafías ya del alemán moderno temprano, da lema al 93,7 % de los tokens (las palabras en rima se lematizan a partir de su `@norm`); lo que falta son sobre todo nombres propios y grafías tardías (*stuend*, *hauss*, *niemand*).
- Las palabras sin lema quedan marcadas en la pestaña **Anotación**, donde se revisa el resultado.
- Los lemas que traiga el archivo tienen prioridad: si cargas la salida del RNNTagger o un TEI con `@lemma`, se usan esos. Si un TEI anotado deja palabras sin `@lemma`, el ReM completa solo esas (opción «completar con ReM los lemas ausentes», activada por defecto).

### Lematizar con el RNNTagger

Para lemas con contexto, el RNNTagger de Helmut Schmid (CIS, LMU München), entrenado también sobre el ReM, desambigua los homógrafos:

1. Pasa el texto (UTF-8, un verso por línea) por el RNNTagger: en línea con WebLicht (<https://weblicht.sfs.uni-tuebingen.de/>, *SfS: To TCF Converter* + *SfS: RNN Tagger*, y en la vista de tabla *Save tokens as CSV*) o con la versión descargable (<https://www.cis.lmu.de/~schmid/tools/HistoricalGermanTools>).
2. Carga el resultado en Mâze: se reconoce solo.
3. El RNNTagger escribe la e de umlaut como *è*; si hace falta, unifica con reglas de normalización (p. ej. `è => e`).

En los dos casos, elige **lema** como unidad de análisis para que frecuencias, riqueza, keyness y estilometría trabajen con lemas. La concordancia busca entonces todas las formas de un lema (y muestra el lema y la categoría de cada aparición), la pestaña Gramática muestra categorías y formas por lema, y SentiMhd 2 puede compararse por lema.

### Revisar la lematización

La pestaña **Anotación** muestra el texto verso a verso con el lema y la categoría debajo de cada palabra, marca las que se han quedado sin lema y permite resaltar o filtrar por lema, forma o categoría (con comodines). Debajo, dos tablas exportables: **lemas y sus formas**, para ver qué ha agrupado el lematizador bajo cada lema, y **formas con más de un lema**, que reúne homógrafos y posibles errores. Al hacer clic en un lema o una forma, el texto se filtra para mostrar solo sus apariciones.

**Convención de nombres** (la de Stylo; los nombres se pueden cambiar en la lista de archivos antes de analizar): `Autor_Titulo.txt`, p. ej. `Hartmann_Erec.txt`, `Wolfram_Parzival.txt`. Lo que va antes del primer guion bajo es la clase (autor). Los archivos que empiezan por `anon_` o `desconocido_` se tratan como de autoría desconocida y la aplicación propone su atribución.

## Qué calcula

**Por texto**
- Recuentos y longitudes de palabra, oración y verso.
- Unas 30 medidas de riqueza léxica: TTR, Guiraud, Herdan, Maas, Brunet, Honoré, Sichel, Yule K/I, Simpson, MATTR, MSTTR, MTLD, HD-D, entropía, etc. Incluyen intervalos de confianza por bootstrap de bloques y un gráfico de estabilidad frente a la longitud.
- Zipf (mínimos cuadrados y Zipf-Mandelbrot), ley de potencia por máxima verosimilitud (Clauset, Shalizi y Newman 2009), espectro de frecuencias y ley de abreviación.
- Heaps con comparación frente al texto barajado; rarefacción exacta y extrapolación del vocabulario con el estimador de Chao (Chao et al. 2014); Chao1.
- **Estructura**: medidas por segmento (partes iguales, expresión regular, divisiones TEI o párrafos), densidad de hápax por segmento, palabras y terminaciones de rima, palabras iniciales de verso y ley de Menzerath-Altmann en dos niveles: oración → palabras → sílabas (y verso → palabras → sílabas) y oración → cláusulas → palabras, la formulación clásica con niveles contiguos. En verso, una nota indica las sílabas por línea y su coeficiente de variación, porque la curva por versos refleja sobre todo la métrica.
- Frecuencias con dispersión D de Juilland, tendencias y gráfico de dispersión, concordancias KWIC con referencia de verso y ordenación por dos claves. Con texto anotado, la concordancia busca también por lema o categoría y admite una **consulta combinada**: condiciones unidas con `&` en la misma palabra (`lemma=haben & pos=V*`) y palabras consecutivas separadas por `;` (`pos=ADJ* ; pos=N*`).
- Colocados (MI, t-score, logDice, G²), red de coapariciones, n-gramas para localizar fórmulas, y nube de palabras.
- **Gramática** (con texto anotado): distribución de categorías, densidad léxica y formas por lema.
- **Anotación** (con texto anotado): el texto con lema y categoría bajo cada palabra y tablas para revisar la lematización (véase más arriba).

**Corpus**
- **Comparación**: tabla de medidas con rarefacción a un tamaño común, intervalos de confianza y curvas de Zipf, Heaps y rarefacción superpuestas. El **subcorpus por metadatos** compara dos grupos de textos definidos por un campo de su ficha (autor, fecha, género, región, manuscrito, procedencia): frecuencias agrupadas, G², log ratio y significación corregida por FDR (Benjamini–Hochberg); los dos grupos pueden pasarse a la estilometría.
- **Keyness**: log-likelihood (G²), BIC, %DIFF y log ratio frente a otro texto o al resto del corpus; útil también para comparar testimonios o redacciones.
- **Estilometría**: Delta de Burrows, de Eder, del coseno y cuadrática sobre las MFW, con culling y muestreo. Los nombres propios (palabras que en los textos van casi siempre con mayúscula fuera de inicio de verso u oración) se excluyen por defecto de los rasgos, y se puede añadir una lista propia de palabras excluidas. Ofrece dendrograma (Ward, UPGMA, completo, simple), PCA con cargas, matriz de distancias, clasificación con validación cruzada (vecino más próximo y centroide) y atribución de textos anónimos.
- **Rolling Delta**: distancia de una ventana móvil del texto objetivo a cada autor de referencia, para detectar cambios de mano, de fuente o interpolaciones. El eje horizontal se da en versos (o párrafos), el recuadro al pasar el ratón indica el tramo que abarca cada ventana y las divisiones del texto se marcan con líneas verticales. En la transmisión manuscrita mhd. la señal mezcla autor y copista: conviene trabajar con texto normalizado o por lema.

**Léxicos y sentimiento**: densidad por categoría, índice de polaridad, arco a lo largo del texto (por segmentos o con ventana móvil), palabras que más contribuyen y comparación entre textos; opcionalmente, comparación por lema e inversión de la polaridad tras una negación. SentiMhd 2 viene con una lista inicial de palabras desactivadas (*wol*, *meinen*, *gewan*, *gerne*, *recht*, *sere*, *l*, *n*, *x*, *xi*), que actúan sobre todo como adverbios, intensificadores u homógrafos; se puede revisar o restablecer, y la elección se guarda. También se puede cargar cualquier otro léxico en TSV (categoría + formas) o una lista de palabras, en UTF-8 o Windows-1252.

**Palabras vacías propias**: en las opciones, «Palabras vacías adicionales» admite escribirlas o cargar una lista en un archivo de texto (una por línea o separadas por comas; las líneas con # se ignoran). Se suman a la lista de la lengua o, si se marca «usar solo esta lista», la sustituyen.

**Normalización ortográfica**: reglas `origen => destino` (también con expresiones regulares). Las reglas y opciones se pueden guardar como perfiles reutilizables, que también se exportan e importan en JSON.

**Fichas de los textos**: cada texto tiene una ficha (botón «Ficha» en la lista de textos) con autor, título, fecha o período, género, región, manuscrito y procedencia. Se muestra en el resumen, alimenta el subcorpus por metadatos y se incluye en las exportaciones.

**Sesiones**: «Guardar sesión completa» descarga un archivo `.maze.json` con los textos originales, las fichas, todos los parámetros y los léxicos propios; «Restaurar sesión» lo vuelve a cargar y recalcula los análisis. Así se puede retomar o compartir un trabajo exactamente con la misma configuración. Una sesión solo se abre en la misma edición de Mâze con la que se guardó.

**Exportación**: cada tabla en CSV o Excel (.xlsx); cada gráfico en SVG o PNG; todas las métricas en JSON o Excel (con una hoja de metadatos si los textos tienen ficha); y un informe HTML con parámetros, metadatos, tablas y gráficos, que también puede imprimirse como PDF.

## Recursos incluidos

- **SentiMhd 2**: Dimpel, Friedrich Michael (2025): *SentiMhd 2*. DARIAH-DE. https://doi.org/10.20375/0000-0016-DADD-A. Licencia CC BY 4.0. Se incluye dentro de `index.html` sin cambios en su contenido (solo se han unificado los saltos de línea). Véase también Dimpel, F. M. (2024): «Entwicklung eines mittelhochdeutschen Sentiment-Wörterbuchs aus korpushermeneutischer Perspektive», *Zeitschrift für Literaturwissenschaft und Linguistik*, https://doi.org/10.1007/s41244-024-00339-0.

- **Referenzkorpus Mittelhochdeutsch (ReM)**: Roussel, Adam; Klein, Thomas; Dipper, Stefanie; Wegera, Klaus-Peter; Wich-Reif, Claudia (2024): *Referenzkorpus Mittelhochdeutsch (1050–1350)*, versión 2.1. https://doi.org/10.5281/zenodo.12825871. ISLRN 937-948-254-174-0. Licencia CC BY-SA 4.0. La tabla de formas, lemas y categorías incluida en `index.html` se ha derivado de la versión JSON del corpus y se distribuye con la misma licencia (CC BY-SA 4.0).

Si publicas resultados obtenidos con SentiMhd 2 o con la lematización del ReM, cita también estos recursos.

## Novedades de la versión 2.2

- Ley de Menzerath en el nivel oración → cláusulas → palabras, y aviso en verso sobre el efecto de la métrica.
- Fichas de metadatos, subcorpus por metadatos y sesiones guardables (`.maze.json`).
- Concordancias por lema y categoría, con consulta combinada.
- El ReM completa los lemas que falten en un TEI anotado sin sobrescribir los del archivo.
- Palabras vacías propias desde un archivo, con la opción de usar solo esa lista.
- La interfaz completa está traducida al alemán y al inglés.


El código de Mâze se publica con licencia MIT (véase `LICENSE`). Los datos incluidos conservan sus licencias: CC BY 4.0 (SentiMhd 2) y CC BY-SA 4.0 (tabla del ReM).
