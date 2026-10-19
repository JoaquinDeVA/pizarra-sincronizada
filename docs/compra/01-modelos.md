# Modelos de placa de 7,0" — encaje y rendimiento

Consultado: 2026-10-20. Parte de la [guía de compra](README.md). Requisitos: ESP32-S3 o P4 con pantalla integrada, táctil capacitivo, wifi, USB-C, sin batería (D5, D7, D10); criterio principal: calidad táctil (D15).

> Los datos de especificaciones vienen de fichas de fabricante/tienda y de [Atomic14](https://www.atomic14.com/esp32/boards/7-inch-displays/) (comparativa comunitaria de julio de 2026). Las valoraciones de "encaje" son mías, a partir de esas fuentes, y no están medidas en hardware.

## Resumen

| Modelo | Pantalla | SoC / memoria | Precio de referencia | Encaje con el proyecto |
|---|---|---|---|---|
| [A. Sunton/Jingcai 8048S070C](#a-sunton--jingcai-esp32-8048s070c) | 800×480 RGB | ESP32-S3, 16 MB flash, 8 MB PSRAM | ~US$21–28 (guía) | Encaja con ajustes |
| [B. Waveshare ESP32-S3-Touch-LCD-7](#b-waveshare-esp32-s3-touch-lcd-7) | 800×480 RGB | ESP32-S3, 16 MB flash, 8 MB PSRAM | US$31,99–36,99 (oficial) | Encaja, mejor soporte |
| [C. Elecrow CrowPanel Advance 7"](#c-elecrow-crowpanel-advance-7) | 800×480 RGB | ESP32-S3, 16 MB flash, 8 MB PSRAM | 49,95 € (Openelab, agotada) | Encaja; sobran funciones |
| [D. Guition JC1060P470C (P4)](#d-guition-jc1060p470c-esp32-p4) | 1024×600 MIPI-DSI | ESP32-P4, 32 MB PSRAM, wifi vía ESP32-C6 | US$34,9 (SpotPear) | Sobra potencia; riesgo de software |
| [E. Waveshare ESP32-S3-Touch-LCD-7B](#e-waveshare-esp32-s3-touch-lcd-7b-referencia) | 1024×600 | ESP32-S3, 16 MB flash, 8 MB PSRAM | 65,84 € (Openelab) · AU$73,26 con GST (Core Electronics) | Fuera de presupuesto |
| [F. Makerfabs MaTouch 7"](#f-makerfabs-matouch-s3-7) | 800×480 o 1024×600 | ESP32-S3, 16 MB flash, 8 MB PSRAM | 59,95 € (Elektor, versión 1024×600) | Fuera de presupuesto |

## Rendimiento común a las placas S3 con panel RGB (A, B, C)

- Las cuatro familias S3 usan bus RGB paralelo a 800×480. Espressif indica ~23 fps de media con LVGL a 800×480 ([FAQ de Espressif](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html)). No es dato de nuestra aplicación.
- El framebuffer vive en PSRAM (≈750 KB por pantalla completa). Con wifi activo puede aparecer parpadeo o jitter si no se ajusta ([Atomic14](https://www.atomic14.com/esp32/boards/sunton-esp32-8048s070/)).
- Casi todos los GPIO van al panel: quedan ~0–4 libres. No afecta a una pizarra, que no necesita más pines.
- *Hipótesis mía:* para dibujar, el trazo nuevo se pinta en local sin esperar a la red y solo se refresca el área modificada, así que 23 fps de media no limita la latencia percibida. Hay que medirla (A2 en [decisiones](../decisiones-de-diseno.md)).

## A. Sunton / Jingcai ESP32-8048S070C

| Dato | Valor |
|---|---|
| Pantalla / táctil | 7" IPS 800×480 RGB (EK9716), GT911 capacitivo de 5 puntos |
| SoC | ESP32-S3 N16R8 (16 MB flash, 8 MB PSRAM) |
| Extras | USB-C, SD, altavoz (amplificador MAX98357 o NS4168 según versión) |
| Batería | No |
| Referencias | [Atomic14](https://www.atomic14.com/esp32/boards/sunton-esp32-8048s070/) · [ESP3D](https://esp3d.io/esp3d-tft/version_1x/hardware/esp32-s3/sunton-70-8048/) · [Tinytronics V0.1](https://www.tinytronics.nl/nl/development-boards/microcontroller-boards/met-wi-fi/jingcai-esp32-8048s070c-v0.1-7-inch-tft-lcd-display-800*480-pixels-met-touchscreen-esp32-s3) |

**Problemas conocidos**
- **Revisiones distintas con código de inicio distinto:** v1.1, v1.3 y v1.4. En Tinytronics, el esquema es de la v1.1, que lleva otro controlador táctil; la V0.1 cambia el chip de audio.
- **Pin INT del GT911 sin conectar** por defecto. Sin él, el procesador pasa del 80 % de uso sondeando el táctil. Se arregla con un puente de 0 Ω en R17 hacia GPIO18 ([ESP3D](https://esp3d.io/esp3d-tft/version_1x/hardware/esp32-s3/sunton-70-8048/)). No consta si ya viene hecho en cada lote.
- **Cuidado con listados confusos:** [Hobbyelectronica](https://www.hobbyelectronica.nl/en/product/7-0-capacitive-touchscreen-esp32-8048s070c/) lista el 8048S070C con **ESP32-S3-WROOM-1-N4R8 (4 MB de flash)** y panel **TN**, no el IPS/N16R8 de las demás fuentes. Antes de comprar, confirmar flash y panel con el vendedor.
- No usar esquemas del 8048S043 (4,3"): sus pines son distintos.

**Encaje (D15):** funcional y el más barato; exige comprobar la revisión y el pin INT. La comunidad es la más extensa en ESPHome, pero el soporte de LVGL/Arduino es comunitario, no oficial.

## B. Waveshare ESP32-S3-Touch-LCD-7

| Dato | Valor |
|---|---|
| Pantalla / táctil | 7" IPS 800×480 RGB, GT911 de 5 puntos |
| SoC | ESP32-S3 N16R8 (16 MB flash, 8 MB PSRAM) |
| Extras | USB-C (más USB-UART), SD, RS485, CAN, expansor CH422G |
| Batería | Conector LiPo PH2.0 con cargador CS8501 (580 mA); la [wiki](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7) recomienda celdas <2000 mAh. Atomic14 dice que no hay batería. Irrelevante para D5 |
| Consumo | 5 V a 450 mA según la wiki (sin desglose) |
| Tamaño | 192,96 × 110,76 mm con táctil |
| Variantes | Con táctil (antena interna o externa) o sin táctil; elegir **Touch** |
| Hojas de datos | [Esquema](https://files.waveshare.com/wiki/ESP32-S3-Touch-LCD-7/ESP32-S3-Touch-LCD-7-Sch.pdf) · [ESP32-S3](https://files.waveshare.com/wiki/common/Esp32-s3_datasheet_en.pdf) · [TRM](https://files.waveshare.com/wiki/common/Esp32-s3_technical_reference_manual_en.pdf) · [WROOM-1](https://files.waveshare.com/wiki/common/Esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf) · [ST7262 (pantalla)](https://files.waveshare.com/wiki/common/ST7262.pdf) · [GT911 (táctil)](https://files.waveshare.com/wiki/common/GT911_EN_Datasheet.pdf) · [CH422G](https://files.waveshare.com/wiki/common/CH422DS1_EN.pdf) |
| Referencias | [Waveshare](https://www.waveshare.com/esp32-s3-touch-lcd-7.htm) · [wiki](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7) · [Atomic14](https://www.atomic14.com/esp32/boards/waveshare-esp32-s3-touch-lcd-7/) |

**Problemas conocidos** ([Atomic14](https://www.atomic14.com/esp32/boards/waveshare-esp32-s3-touch-lcd-7/))
- **Flash y PSRAM comparten SPI1:** una escritura en flash puede detener el panel y causar jitter visible. Mitigación: PSRAM octal a 120 MHz con XIP-from-PSRAM, o ESP-IDF.
- ~4 GPIO libres; el resto de entradas/salidas pasan por el CH422G.
- Las demos de Waveshare son monolíticas; no mezclar configuraciones con los modelos hermanos 5B, 4,3" o 5".
- **El INT del táctil sí está conectado:** TP_IRQ va a GPIO4, SDA a GPIO8 y SCL a GPIO9; el reset (TP_RST) pasa por el expansor CH422G (EXIO1) ([wiki](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7)). Es la ventaja frente al Sunton para D15.
- La wiki da 16 MB de flash en un apartado y 8 MB en otro; el módulo es N16R8 (16 MB), pero confirmarlo en la placa que llegue.

**Encaje (D15):** la opción con mejor documentación oficial (esquema y hojas de datos publicados), pin INT conectado y comunidad; Atomic14 la recomienda por tiempo de integración. Cuesta ~US$10 más que la A.

## C. Elecrow CrowPanel Advance 7"

| Dato | Valor |
|---|---|
| Pantalla / táctil | 7" IPS 800×480 RGB, capacitivo (controlador del panel SC7277) |
| SoC | ESP32-S3 (16 MB flash, 8 MB PSRAM) |
| Extras | Micrófono, altavoz, SD, zócalo de radio intercambiable (wifi 6 con C6, Zigbee, LoRa) |
| Batería | No |
| Variantes | Con o sin caja acrílica (la electrónica es la misma) |
| Referencias | [Atomic14](https://www.atomic14.com/esp32/boards/elecrow-crowpanel-advance-7/) · [Openelab](https://openelab.io/a/s/products/elecrow-crowpanel-advance-7-hmi-esp32-s3-ai-touch-screen) |

**Problemas conocidos**
- SD, audio y radio comparten GPIO por interruptores DIP: algunas funciones son excluyentes.
- Las revisiones v1.0/v1.1 no tienen PWM de retroiluminación (sin atenuar).
- El driver del panel no está en ESPHome estándar; Elecrow publica ejemplos oficiales (enero de 2026).
- Soporte oficial de LVGL/Arduino y tutoriales de SquareLine.
- No la confundir con el CrowPanel "Basic" 7": varios anuncios la describen con panel TN y 4 MB de flash.

**Encaje (D15):** funciona y tiene soporte oficial, pero paga por funciones que el proyecto no usa (micro, altavoz, radios). Menos stock en tiendas UE.

## D. Guition JC1060P470C (ESP32-P4)

| Dato | Valor |
|---|---|
| Pantalla / táctil | 7" IPS **1024×600** por MIPI-DSI, capacitivo (chip no nombrado) |
| SoC | ESP32-P4, **32 MB de PSRAM**; el wifi 6 y BLE los da un ESP32-C6 vía ESP-Hosted |
| Extras | USB-C, SD |
| Batería | No |
| Variantes | **C** = con táctil; N = sin táctil. El tamaño de flash varía según el anuncio |
| Referencias | [Atomic14](https://www.atomic14.com/esp32/boards/guition-jc1060p470/) · [CNX Software](https://www.cnx-software.com/2025/05/06/7-inch-esp32-p4-wireless-touchscreen-display-supports-guition-designer-for-lvgl-arduino-and-esp-idf-programming/) · [SpotPear](https://spotpear.com/shop/ESP32-P4-Display-7inch-TouchScreen-JC1060P470C.html) |

**Problemas conocidos**
- Soporte de ESPHome en curso y soporte de P4 en arduino-esp32 en beta; la ruta recomendada es ESP-IDF con LVGL.
- Documentación escasa: la comunidad es el manual.
- La revisión 3.0 del silicio P4 (2026) cambia los carriles de alimentación; los lotes tempranos y tardíos pueden diferir.
- SpotPear lo marca como "Off-production".

**Encaje (D15):** potencia de sobra y más resolución, que es la ruta con más margen para un táctil fluido. *Hipótesis mía, sin medir.* A cambio, mayor riesgo y esfuerzo de software, y wifi vía coprocesador.

## E. Waveshare ESP32-S3-Touch-LCD-7B (referencia)

7" 1024×600, con monitorización de batería y conector LiPo según [Atomic14](https://www.atomic14.com/esp32/boards/7-inch-displays/). [Openelab](https://openelab.io/es/products/waveshare-esp32-s3-7inch-lcd) lo vende a 65,84 € con IVA (consulta del 2026-10-20, envío "en 72 horas"). Queda fuera del presupuesto; se anota solo como referencia.

## F. Makerfabs MaTouch S3 7"

| Dato | Valor |
|---|---|
| Pantalla / táctil | 7" IPS en dos versiones, 800×480 y 1024×600; RGB565; GT911 de 5 puntos |
| SoC | ESP32-S3-WROOM-1, 16 MB flash, 8 MB PSRAM |
| Extras | Dos USB-C, SD, dos conectores Grove/Mabee |
| Precio | 59,95 € en [Elektor](https://www.elektor.nl/products/matouch-esp32-s3-parallel-tft-7-touch-display-1024x600) (versión 1024×600, con IVA); en Elektor.de aparecía a 44,95 € pero sin stock; Tindie 79,80 US$ |
| Referencias | [CircuitPython](https://circuitpython.org/board/makerfabs_tft7/) · [Tindie](https://www.tindie.com/products/makerfabs/matouch-esp32-s3-parallel-tft-with-touch-7/) |

No he encontrado hoja de datos ni esquema enlazados; hay que revisar la wiki de Makerfabs. Precio fuera del presupuesto.

## Sin datos suficientes

- **Viewe UEDX80480070E-WB-A** (ESP32-S3 de 7"): no localicé ficha ni precio; solo hay un BSP del modelo de 4,3" en el [registro de componentes de Espressif](https://components.espressif.com/components/viewesmart/bsp_uedx80480043_wb_a/versions/1.0.0/readme). Consultar a Viewe.

## Descartadas

| Placa | Motivo |
|---|---|
| Waveshare ESP32-P4-WIFI6-Touch-LCD-7 | US$80–90, muy por encima del presupuesto |
| Elecrow CrowPanel "Basic" 7" | Anuncios que mezclan TN y 4 MB de flash; la Advance es la variante clara |
| Makerfabs MaTouch 7" | No evaluada en esta pasada |
