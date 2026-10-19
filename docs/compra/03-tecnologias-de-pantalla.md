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
- Cada actualización parcial tarda 0,4–1,5 s según el panel. Un trazo dibujado a mano se vería a saltos; contradice el criterio de D15.
- Tras varias actualizaciones parciales aparece imagen residual; una guía recomienda un refresco completo cada 5–10 parciales *(fuente secundaria: [Makerguides](https://www.makerguides.com/partial-refresh-e-paper-display-esp32/))*. LilyGo avisa de que no debe refrescarse parcialmente de forma prolongada sin hacer uno completo.
- No he encontrado un panel e-paper de 7" con táctil capacitivo: los táctiles localizados son de 4,7" y 6".
- Reseñas del LilyGo T5-4.7-S3 mencionan sobrecalentamiento con uso continuo y táctil poco fino ([LilyGo](https://www.lilygo.cc/en-ca/products/t5-4-7-inch-e-paper)); no verificadas.

## Opciones para el tema del brillo y la presencia (sin cambiar de tecnología)

- Brillo bajo y apagado por inactividad, ya contemplado en D25.
- Encender la pantalla al tocarla o cuando llega un trazo de la otra pantalla.
- Fondo "negro" en IPS no apaga el panel; solo oscurece la imagen.

## Lo no investigado

Tinta "magnética" electrónica real (no la de juguete), pantallas OLED de 7", paneles reflectivos de color, ni precios actuales de e-paper táctiles en tiendas UE.
