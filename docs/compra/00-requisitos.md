# Requisitos de hardware: dimensionamiento del problema

Consultado: 2026-10-20. Parte de la [guía de compra](README.md). Responde a "qué necesita el programa" antes de elegir placa.

> Las cifras de memoria de la aplicación son **estimaciones mías** salvo donde se cita fuente. Se confirman midiendo en una placa real (`esp_get_free_heap_size()` antes y después de conectar).

## Qué tiene que hacer el aparato

Mostrar un lienzo, leer el táctil, dibujar el trazo en local, mantener una conexión wss con el servidor y repintar lo que llega del otro lado. Nada más (D15).

## Memoria

| Concepto | Memoria | Dónde vive | Fuente |
|---|---|---|---|
| Framebuffer 480×272 (RGB565) | 261 KB | PSRAM | cálculo: ancho×alto×2 B |
| Framebuffer 800×480 | **750 KB** | PSRAM | cálculo |
| Framebuffer 1024×600 | **1,2 MB** | PSRAM | cálculo |
| Segundo framebuffer (contra *tearing*) | ×2 de lo anterior | PSRAM | Espressif lo usa para el panel RGB |
| Registro de trazos (4 B/punto, 50.000 puntos) | ~200 KB | PSRAM | estimación |
| Una conexión TLS (mbedTLS) | ~22–42 KB de heap | RAM interna | [Espressif](https://docs.espressif.com/projects/esp-idf/en/v5.4/esp32/api-reference/protocols/mbedtls.html): 42 KB por defecto y 22 KB con buffers dinámicos, en un ejemplo HTTPS |
| Pila wifi + buffers de WebSocket | decenas de KB | RAM interna | estimación |
| LVGL, si se usa | decenas a cientos de KB | RAM interna/PSRAM | estimación |
| Firmware (wifi + TLS + gráficos) | ~1–2 MB de flash | Flash | estimación |
| Firmware con actualización OTA | ×2 de lo anterior | Flash | estimación |

**Qué significa:**
- A 800×480, **unos 2 MB de PSRAM bastan** para un framebuffer, el registro y el resto con holgura. Las placas de 7" traen 8 MB: **4× de margen**. En 1024×600 con dos framebuffers (~2,4 MB) siguen sobrando.
- El cuello no es la PSRAM sino la **RAM interna** (512 KB en el ESP32-S3, [Waveshare wiki](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7)): ahí compiten la pila wifi, TLS y los buffers DMA. Una sola conexión TLS es asumible.
- El flash de 16 MB sobra. **4 MB bastarían sin OTA y quedarían justos con OTA.** Si la actualización por aire entra en alcance, conviene ≥8 MB.

## Procesador y bus

- Dos núcleos a 240 MHz sobran: la carga es leer el táctil (≲200 Hz), pintar segmentos y enviar ~30 paquetes/s.
- La interfaz de pantalla importa más que la RAM. Espressif recomienda SPI hasta 480×320 y RGB paralelo a 480×480 y 800×480; con RGB a 800×480 el LVGL medio es ~23 fps ([FAQ de Espressif](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html)). Un panel de 7" exige S3 con RGB o P4 con MIPI-DSI; el ESP32 clásico no sirve.

## Requisitos mínimos para elegir placa

| Requisito | Mínimo | Por qué |
|---|---|---|
| SoC | ESP32-S3 o ESP32-P4 | Único camino a 7" fluido |
| PSRAM | 4 MB (8 MB recomendado) | Framebuffer + registro con holgura |
| Flash | 8 MB (16 MB recomendado) | OTA futura |
| Táctil | Capacitivo con el pin de interrupción conectado a un GPIO | Evita sondear y gastar CPU (D15) |
| Panel | IPS | Ángulo de visión |
| Documentación | Esquema y hojas de datos publicados | Poder cablear el INT, ajustar el RGB y diagnosticar |

## Lo que no hace falta

Cámara, audio, SD, RS485/CAN, batería, radios extra. Las placas que los llevan no aportan nada, pero tampoco molestan salvo por consumo y por GPIO compartidos.

## ¿Escribir el programa antes de elegir hardware?

Parte del programa **no depende de la placa**: servidor, protocolo, cliente web de prueba y la lógica del lienzo y los trazos. Lo único ligado al hardware es el arranque de la pantalla y del táctil. Esto permite, si se decide así (A7), trabajar sin comprar y medir los requisitos reales antes de gastar.
