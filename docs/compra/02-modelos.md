# Modelos de e-ink a color (registro)

Consultado: 2026-10-24. Resumen en [README.md](README.md). Requisitos en [01-requisitos.md](01-requisitos.md). Precios y tiendas en [03-vendedores.md](03-vendedores.md).

Criterios: e-ink a color, ≥5", ESP32, batería, acceso de desarrollo (D5, D7, D30, D32). "Visto" = leído en la ficha o wiki el 2026-10-24; "sin verificar" = fragmento de búsqueda.

## Resumen

| Modelo | Pantalla | Batería | Caja | Apertura (¿se puede programar?) | Precio de referencia |
|---|---|---|---|---|---|
| [A. Waveshare ESP32-S3-PhotoPainter](#a-waveshare-esp32-s3-photopainter-73) | 7,3" 6 colores 800×480 | Opcional (1500 mAh) | Marco de madera con soporte | Abierta: ESP-IDF, GitHub, esquema | US$82,99–83,99 |
| [B. Seeed XIAO ePaper 7,3" DIY Kit (EE04)](#b-seeed-xiao-epaper-73-diy-kit-ee04) | 7,3" 6 colores 800×480 | Conector con cargador, **sin batería incluida** | **Sin caja** | Abierta: kit de desarrollo; ESPHome, soporte de terceros | 67,15 € |
| [C. Seeed reTerminal E1002](#c-seeed-reterminal-e1002) | 7,3" 6 colores 800×480 | 2000 mAh incluida | Caja con soporte | Abierta: Arduino, ESP-IDF, PlatformIO, ESPHome | US$99 |
| [D. Seeed reTerminal E1004](#d-seeed-reterminal-e1004-133) | 13,3" 6 colores 1200×1600 | 5000 mAh | Caja y soporte (376×311×40 mm) | Igual que la C | 304,45 € (Openelab, preventa) · US$279,90 |
| [E. Soldered Inkplate 6COLOR](#e-otras-fuera-de-presupuesto-o-sin-evaluar) | 5,8" 7 colores 600×448 | Sí | — | Arduino | 209,95 € |

## A. Waveshare ESP32-S3-PhotoPainter 7,3"

| Dato | Valor |
|---|---|
| SoC | ESP32-S3-WROOM-1-N16R8: 16 MB flash, 8 MB PSRAM, wifi 2,4 GHz, BLE 5 |
| Pantalla | 7,3" E Ink Spectra 6, 800×480 |
| Batería | Opcional, 3,7 V; la ficha menciona 1500 mAh. Variantes con y sin batería |
| Botones | KEY (función libre), BOOT (modo de descarga), PWR (encendido) |
| Otros | Ranura TF (FAT32, tarjeta de 16 GB incluida), RTC PCF85063, sensor SHTC3, micrófonos dobles, altavoz de 2 W incluido |
| Caja | Marco de madera maciza con borde blanco, soporte trasero giratorio y colgador |
| Reposo | La ficha dice "ultra-long standby" sin dar corriente; la wiki dice ≤1 mA |
| Precio | US$82,99–83,99 según la opción de batería |
| Fuentes | [Ficha](https://www.waveshare.com/esp32-s3-photopainter.htm) · [Wiki](https://www.waveshare.com/wiki/ESP32-S3-PhotoPainter) |

**Apertura (visto en la wiki):**
- Firmware en C/C++ sobre **ESP-IDF** (CMake). Ejemplo de código en zip y repositorio [wurongmin/ESP32-S3-PhotoPainter](https://github.com/wurongmin/ESP32-S3-PhotoPainter), esquema en PDF.
- La ficha dice que el código es abierto y da "más libertad para personalizar". La wiki aclara: "sin soporte técnico oficial".
- Se flashea por USB-C con el flasher web de Espressif o la herramienta Flash Download; modo de descarga: mantener PWR 5 s, mantener BOOT y pulsar PWR.
- Firmware de serie: 3 modos (imágenes de la tarjeta SD, servidor en LAN en 192.168.4.1 y asistente de IA tipo Xiaozhi). Una fuente de terceros dice que muchas unidades llegan con el asistente de IA.
- Firmwares de terceros que lo soportan: [esp32-photoframe](https://github.com/aitjcize/esp32-photoframe) (MIT) y un ejemplo ESP-IDF con LVGL 9 y tramado Floyd–Steinberg en el [registro de Espressif](https://components.espressif.com/components/tuanpmt/esp_epaper/versions/2.0.0/examples/esp32s3_photopainter).

**No verificado:** si el marco permite abrir la placa o si el USB-C queda accesible con el marco puesto.

## B. Seeed XIAO ePaper 7,3" DIY Kit (EE04)

| Dato | Valor |
|---|---|
| SoC | XIAO ESP32-S3 Plus (memoria no listada en la ficha de tienda) |
| Pantalla | 7,3" Spectra 6, 800×480; la placa admite paneles de 24 y 50 pines (puentes) |
| Batería | Conector JST 2,0 mm con interruptor y cargador integrado; **no incluye batería** |
| Botones | 1 reset y 3 de usuario |
| Incluye | Placa EE04, antena FPC 2,4 GHz (2,9 dBi) y la pantalla de 7,3". **Sin batería, caja ni cable** |
| Precio | 67,15 € en Openelab (preventa) |
| Fuentes | [Openelab](https://openelab.io/products/seeed-studio-xiao-epaper-7-3-inch-diy-kit) · [CNX Software](https://www.cnx-software.com/?p=161052) |

**Apertura:**
- Es un kit de desarrollo por diseño: placa XIAO, botones y conector de batería.
- Hay ejemplos de [ESPHome](https://wiki.seeedstudio.com/EE04_with_esphome_advanced/) (ESP-IDF o Arduino), pero para una pantalla de 2,9", no la de 7,3".
- Soportado por [esp32-photoframe](https://github.com/aitjcize/esp32-photoframe).
- **No encontré** un ejemplo oficial de Arduino o ESP-IDF para el panel de 7,3".

## C. Seeed reTerminal E1002

| Dato | Valor |
|---|---|
| SoC | ESP32-S3, 8 MB PSRAM, 32 MB flash |
| Pantalla | 7,3" Spectra 6 800×480; sin parcial; 15–20 s |
| Batería | 2000 mAh incluida; ~3 meses |
| Caja | 176 × 120 × 53 mm con soporte |
| Otros | USB-C 5 V/1 A, microSD, sensores de temperatura y humedad, zumbador, 3 botones, cabecera de 8 pines |
| Precio | US$99 de MSRP |
| Fuente | [Wiki Seeed](https://wiki.seeedstudio.com/reterminal_e10xx_main_page/) |

**Apertura (visto en la wiki):** firmware de serie SenseCraft Seeedash; soporta ESPHome/Home Assistant, Arduino, PlatformIO, ESP-IDF y TRMNL. Soportado por esp32-photoframe.

## D. Seeed reTerminal E1004 13,3"

| Dato | Valor |
|---|---|
| Pantalla | 13,3" Spectra 6, 1200×1600 (≈203×270 mm, vertical; calculado), sin parcial, ~20 s |
| Batería | 5000 mAh, ~6 meses con un refresco cada 6 h según la wiki; otro texto de Seeed habla de un año a un refresco al día |
| Tamaño del producto | 376 × 311 × 40 mm |
| Incluye | Pantalla, cable USB-C, soporte con tornillo, destornillador y 2 ganchos para colgar |
| Precio | 304,45 € con IVA en [Openelab](https://openelab.io/products/seeed-studio-reterminal-e1004-epaper) (preventa, despacho 10–20 días, reposición estimada 30-nov-2026, DDP en la UE, envío gratis por encima de 100 €); 410,66 € con IVA en RS (sin stock, envío en diciembre de 2026); US$279,90 de MSRP |
| Resto | Igual que la E1002 (ESP32-S3, 8 MB PSRAM, 32 MB de flash, microSD, 3 botones; abierta con Arduino, ESP-IDF, PlatformIO y ESPHome; ESPHome 2026.7.0 o superior para esta serie) |
| Fuentes | [Wiki](https://wiki.seeedstudio.com/reterminal_e10xx_main_page/) · [Openelab](https://openelab.io/products/seeed-studio-reterminal-e1004-epaper) · [Hackster](https://www.hackster.io/news/seeed-packs-its-13-3-color-epaper-display-an-espressif-esp32-and-battery-into-the-reterminal-e1004-dcc5ed526bfb) |

No encontré una reseña independiente de su calidad de imagen; lo que hay es lenguaje de Seeed.

## E. Otras, fuera de presupuesto o sin evaluar

| Producto | Datos | Motivo |
|---|---|---|
| Waveshare 13,3" E6 con placa ESP32-S3 | 13,3" Spectra 6; US$253,87–259,99 | Sin evaluar a fondo |
| M5Stack PaperColor | 4" Spectra 6 con ESP32 | Menos de 5" |
| Soldered Inkplate 6COLOR | 5,8" 7 colores 600×448, ~12 s, sin parcial, ESP32 WROVER, 209,95 €, plazo ~6 semanas ([Soldered](https://soldered.com/products/inkplate-6color)) | Presupuesto |
| Waveshare 5,65" ACeP 7 colores 600×448 (en bruto) | 69,95 € en [Elektor](https://elektor.com/products/waveshare-5-65-acep-7-color-e-paper-e-ink-display-module-600x448); £69,10 en The Pi Hut (agotada). Necesita una placa ESP32 aparte (~19 €); la tabla de paneles de la placa universal de Waveshare **no lista** el 5,65" ni el 7,3"; hay un controlador comunitario ESP-IDF ([WS565F](https://components.espressif.com/components/codewitch-honey-crisis/htcw_ws565f/versions/0.3.1/readme)) | Dos piezas, compatibilidad sin confirmar; sin ejemplo oficial |
| Pimoroni Inky Frame 5,7" | 5,7" 7 colores ACeP 600×448; ~30 s de refresco; ya montada, con conector de batería, ranura SD y RTC; £79,80 (o £67,83 con descuento) en [The Pi Hut](https://thepihut.com/products/inky-frame-5-7-pico-w-aboard); kit con patas, 3 pilas AA y SD de 16 GB: £105,41 ([RobotShop UK](https://uk.robotshop.com/products/inky-frame-57-inch-pico-w-aboard-inky-frame-accessory-kit), agotado) | **No es ESP32** (Raspberry Pi Pico W, RP2040): choca con D7 |
| Waveshare 7,3" HAT (E) | Versión para Raspberry Pi, AU$139,95 | No es ESP32 |
| Good Display GDEP073E01 | Panel de 7,3" Spectra 6; precio y placa no localizados | Sin datos |
| Seeed reTerminal E1001/E1003 | Monocromo | No es color |
| Tricolor negro/blanco/rojo | Waveshare 7,5" (B), 46,95–50,57 € más placa ESP32 | Se asume que "a color" excluye los tricolor |

## Otras tecnologías de color (por qué Spectra 6)

| Tecnología | Lo que dicen las fuentes | ¿Placa ESP32 lista? |
|---|---|---|
| **Spectra 6** | Color más cercano al papel; refresco de decenas de segundos (~12 s); recomendable para pantallas estáticas ([liseuses.net](https://www.liseuses.net/e-ink-spectra-6/), [The eBook Reader](https://blog.the-ebook-reader.com/?p=25028)) | Sí (los kits de esta guía) |
| Gallery 3 | Color mejor que Kaleido 3, pero blancos más oscuros y texto más irregular; refresco <1 s | No encontré ninguna |
| Kaleido 3 | Filtro de color sobre B/N, 150 ppi de color; el color más flojo y más rápido; pensado para lectores | No encontré ninguna ([CNX Software](https://www.cnx-software.com/news/e-ink/page/5/?amp=1)) |

Conclusión: para una pantalla estática de la mejor calidad con placa ESP32 disponible, la opción es Spectra 6.

