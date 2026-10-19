# Tecnologías de pantalla alternativas (registro)

Consultado: 2026-10-22. Registro de datos para decidir A5 (tecnología de pantalla). Resumen en [README.md](README.md). LCD de 7" actual: [01-modelos.md](01-modelos.md).

Convención: *(estimación)* = mía. "Fuente" = enlace de donde sale el dato. Sin verificar = leído en un resultado de búsqueda sin abrir la página.

## Preocupaciones que motivan el registro

1. Consumo de una pantalla LCD normal.
2. Que una pantalla LCD encendida llame la atención en casa.

## Consumo de la LCD actual (Waveshare ESP32-S3-Touch-LCD-7)

| Dato | Valor | Fuente |
|---|---|---|
| Consumo | 5 V × 450 mA = 2,25 W (sin desglose por subsistema) | [Wiki Waveshare](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7) |
| Energía encendida 24 h/día | 2,25 W × 24 h × 365 = ~19,7 kWh/año | cálculo |
| Coste a 0,15–0,25 €/kWh | ~3–5 €/año por aparato | cálculo con precio de la luz *(estimación)* |
| Con apagado por inactividad (D25) | menos; sin medir | — |
| Luz | Panel IPS con retroiluminación: el fondo negro sigue iluminado | conocimiento general |

**Lectura:** el consumo eléctrico es marginal. Lo que cuenta es el brillo del panel encendido, que se mitiga con brillo bajo y apagado por inactividad (D25).

## Tecnologías

| Tecnología | Producto | Tamaño / resolución | Refresco | Consumo | Táctil | Precio | Encaja con D10 (7") / D15 (latencia) |
|---|---|---|---|---|---|---|---|
| **LCD IPS** (actual) | Waveshare ESP32-S3-Touch-LCD-7 | 7" 800×480 | ~23 fps medios LVGL ([Espressif](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html)) | 2,25 W | GT911 5 puntos | ver [02](02-vendedores.md) | Sí / Sí |
| **E-paper B/N en bruto** | Waveshare 7.5" e-Paper | 7,5" 800×480 | 0,4 s parcial · 1,5 s rápido · 4 s completo ([Waveshare](https://www.waveshare.com/7.5inch-e-Paper.htm)) | 48 mW típico al refrescar (variante roja/negra/blanca) | **No** (sin capa táctil en los resultados) | no consultado | Tamaño sí / táctil no, latencia 0,4 s |
| **E-paper con táctil, ESP32-S3** | LilyGo T5-4.7-S3 | 4,7" 960×540, 16 grises | lento; el parcial deja imagen residual ([Atomic14](https://www.atomic14.com/esp32/boards/lilygo-t5-47-epaper-s3/)) | no listado | GT911 de 2 puntos, según SKU | 35–45 US$ (calle), 55 € (Openelab), 39,99 US$ en LilyGo (agotado) | No (4,7") / dudosa |
| **E-paper con táctil, ESP32** | M5Paper V1.1 | 4,7" 960×540, 16 grises | no hallado | batería 1150 mAh | GT911 | £77–82, **descatalogada** | No / dudosa |
| **E-paper con táctil** | Inkplate 6PLUS | 6" 1024×758 | 0,64 s parcial · 1,27 s completo ([CNX Software](https://cnx-software.com/?p=88531)) | 22 µA en reposo | 2 puntos, con luz frontal | 159 US$ en 2021; precio actual no hallado | No (6") / dudosa |
| **Tableta e-ink Android** | Boox Go 7 | 7" | sin datos de latencia | no consultado | Lápiz propio (InkSense), no EMR | 249–349 €, lápiz aparte | Tamaño sí; no es ESP32 (D7) |
| **LCD de memoria (Sharp)** | Adafruit Memory LCD | hasta 2,7" 400×240 | rápido ("como LCD") | muy bajo | **No** | no consultado | No (tamaño) |
| **Tableta de escritura LCD / pizarra magnética** | Boogie Board, Magna Doodle | — | — | — | — | — | Sin investigar; por conocimiento general no se sincronizan por red |

## Observaciones sobre e-paper

- El e-paper mantiene la imagen sin alimentación y gasta solo al refrescar. Esa ventaja pesa menos aquí: D5 elige USB-C constante, no batería.
- Cada actualización parcial tarda 0,4–1,5 s según el panel. Con D31 ya no es un problema: el dibujo ocurre en el móvil.
- Tras varias actualizaciones parciales aparece imagen residual; una guía recomienda un refresco completo cada 5–10 parciales *(fuente secundaria: [Makerguides](https://www.makerguides.com/partial-refresh-e-paper-display-esp32/))*. LilyGo avisa de que no debe refrescarse parcialmente de forma prolongada sin hacer uno completo.
- No he encontrado un panel e-paper de 7" con táctil capacitivo: los táctiles localizados son de 4,7" y 6".
- Reseñas del LilyGo T5-4.7-S3 mencionan sobrecalentamiento con uso continuo y táctil poco fino ([LilyGo](https://www.lilygo.cc/en-ca/products/t5-4-7-inch-e-paper)); no verificadas.

## Opciones para el tema del brillo y la presencia (sin cambiar de tecnología)

- Brillo bajo y apagado por inactividad, ya contemplado en D25.
- Encender la pantalla al tocarla o cuando llega un trazo de la otra pantalla.
- Fondo "negro" en IPS no apaga el panel; solo oscurece la imagen.

## Lo no investigado

Tinta "magnética" electrónica real (no la de juguete), pantallas OLED de 7", paneles reflectivos de color, ni precios actuales de e-paper táctiles en tiendas UE.

## E-paper de 5" o más para un cuadro sin táctil (consultado 2026-10-23)

Medidas calculadas con píxeles cuadrados (±5 %). Precios sin envío ni impuestos de importación salvo que se diga. "Sin verificar" = resultado de búsqueda.

| Opción | Pantalla | ESP32 | Refresco | Precio | Notas |
|---|---|---|---|---|---|
| **Seeed reTerminal E1001** | 7,5" B/N 800×480 (163×98 mm), 4 grises | ESP32-S3, 8 MB PSRAM, 32 MB flash; ya montada, con soporte (176×120×53 mm) | Parcial: sí. "2–5 s" en una sola fila, sin separar parcial y completo | MSRP US$69; ¥14.425 en Switch Science (Japón) | USB-C 5 V/1 A, batería 2000 mAh, microSD, sensores. Firmware propio: Arduino, ESP-IDF, PlatformIO. [Wiki](https://wiki.seeedstudio.com/reterminal_e10xx_main_page/) · [CNX](https://www.cnx-software.com/2025/09/06/reterminal-e1001-e10) |
| Seeed reTerminal E1002 | 7,3" color Spectra 6 800×480 | ESP32-S3 | **Sin parcial**, 15–20 s | US$99 | Aceptable con D31; 6 colores |
| Seeed reTerminal E1003 | 10,3" B/N 1404×1872 (157×209 mm, vertical), 16 grises | ESP32-S3 | Parcial: sí; 2–3 s | US$159,90 | Táctil solo con el firmware de Seeed |
| **Waveshare 7,5" e-Paper + placa ESP32** | 7,5" B/N 800×480, 4 grises | Placa ESP32 aparte (conexión FPC, sin soldar) | 0,4 s parcial · 1,5 s rápido · 4 s completo ([Waveshare](https://www.waveshare.com/7.5inch-e-Paper.htm)) | Pantalla 55 € con IVA en [Tinytronics](https://www.tinytronics.nl/shop/en/display/e-ink/waveshare-7.5-inch-e-ink-e-paper-display) (sin stock, 2–4 semanas) + placa ESP32 ~18,5–19 € ([BerryBase](https://www.ebay.de/p/9039625136), [Elektronicavoorjou](https://elektronicavoorjou.nl/en/?p=39175529)) | Son dos piezas que se enchufan; sin caja ni soporte. Choca con D7 ("no se monta hardware propio") si se cuenta como montaje |
| **Elecrow CrowPanel 5,79"** | 5,79" B/N **272×792** (139×48 mm: franja de 2,9:1) | ESP32-S3; ya montada | Parcial y rápido, según Elecrow; tiempos no publicados | US$31,90 (Elecrow); 39,95 € ([Openelab](https://openelab.io/a/s/products/elecrow-crowpanel-esp32-5-79inch-e-paper-hmi-display-272x792), "sold out") | Conector de batería. La proporción hace pequeño el lienzo si es tipo 16:10 |
| Good Display GDEY075T7 | 7,5" B/N 800×480, 4 grises | Placa ESP32 de Good Display (GD02002) con adaptador GD09003 | 0,34 s parcial · 1,5 s rápido · 3,5 s completo ([eckstein-shop](https://eckstein-shop.de/ESP32_1__75_10)) | No localizado | Compatibilidad del adaptador sin confirmar |
| Soldered Inkplate 10 | 9,7" B/N 1200×825 (203×140 mm), 4 grises | ESP32 WROVER, 8 MB PSRAM | 0,62 s parcial · 1,61 s completo ([CNX](https://cnx-software.com/?p=84501)) | 151,96 € (oferta caducada) / 189,95 € ([Soldered](https://soldered.com/products/inkplate-10)) | Empresa croata (UE). Plazo de stock ~6 semanas |

**Notas**
- Los tiempos son de las hojas de datos o de comparativas; no están medidos con nuestro software.
- Para solo mirar, un parcial de 0,4–1,5 s por actualización es aceptable si se agrupan los trazos, y hace falta un refresco completo periódico para limpiar la imagen residual.
- No investigados: TRMNL, Waveshare 5,83" y 5,65", LilyGo T5 de 7,5".

### Con refresco lento aceptado (D31): color y tricolor (consultado 2026-10-23)

| Opción | Pantalla | ESP32 | Refresco | Precio | Notas |
|---|---|---|---|---|---|
| **Waveshare ESP32-S3-PhotoPainter 7,3" E6** | 7,3" **6 colores** (Spectra 6) 800×480 | ESP32-S3; ya montada, con marco de madera maciza; batería opcional | 12 s según la ficha de la pantalla; 25 s según el manual de la versión HAT (E); sin datos de parcial | US$82,99–83,99 (moneda no indicada en la ficha) | Es un marco para el escritorio (D29). El manual de la pantalla recomienda ≥180 s entre refrescos y uno completo al menos cada 24 h. [Manual HAT (E)](https://www.waveshare.com/wiki/7.3inch_e-Paper_HAT_(E)_Manual) |
| Seeed reTerminal E1002 | 7,3" 6 colores 800×480 | ESP32-S3, 8 MB PSRAM | 15–20 s, sin parcial | US$99 | Ya montada, con soporte y batería. [Wiki](https://wiki.seeedstudio.com/reterminal_e10xx_main_page/) |
| Soldered Inkplate 6COLOR | 5,8" 7 colores 600×448 | ESP32 WROVER | ~12 s, sin parcial | 209,95 € ([Soldered](https://soldered.com/products/inkplate-6color)) | Plazo de stock ~6 semanas; muy caro |
| Waveshare 5,65" ACeP (en bruto) | 5,65" 7 colores 600×448 | Placa ESP32 aparte (~US$14–15, compatibilidad sin confirmar) | 12–30 s | £69,10 en [The Pi Hut](https://thepihut.com/products/5-65-acep-7-colour-e-paper-display-module) | Dos piezas |
| Waveshare 7,5" B/N/**rojo** (en bruto) | 7,5" 3 colores 800×480 | Placa ESP32 aparte (~19 €) | 16 s rápido · 26 s completo | 46,95–50,57 € en Waveshare (UE); 58 € en Tinytronics; 70,74 € en Voelkner | Total ~66–77 € con la placa; no baja de la E1001 |

**Implicaciones**
- Con 6 colores el cliente solo puede mostrar esos colores en el cuadro: negro, blanco, rojo, amarillo, verde y azul. La paleta de D26 se limita a ellos o se reduce al más cercano (A9).
- La guía de ≥180 s entre refrescos limita cuánto "en vivo" se puede mostrar: o se actualiza cuando quien dibuja termina, o cada cierto tiempo (A8).
- Con refresco lento, el servidor puede componer la imagen y mandar un bitmap ya reducido a la paleta, y el firmware se reduce a descargarlo y pintarlo (A8).
- Con D31 **no baja el precio:** nada de lo hallado cuesta menos de ~US$69. Lo que se abre es el color.

