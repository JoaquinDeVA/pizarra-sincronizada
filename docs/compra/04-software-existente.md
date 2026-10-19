# Software existente para el cuadro (registro)

Consultado: 2026-10-24. Resumen en [README.md](README.md). Es lo que se podría reutilizar para el firmware (D8).

## esp32-photoframe

Repositorio: [aitjcize/esp32-photoframe](https://github.com/aitjcize/esp32-photoframe). Descrito como firmware con muchas funciones para pantallas e-paper con ESP32.

| Dato | Valor |
|---|---|
| Licencia | MIT (barra lateral). El README añade que se basa en el código de ejemplo de Waveshare PhotoPainter y remite al proyecto original para su licencia |
| Placas soportadas | Waveshare PhotoPainter 7,3"; Seeed XIAO EE02, EE03 y EE04; Seeed reTerminal E1002, E1003, E1004; M5Paper v1.1 |
| De dónde saca las imágenes | Tarjeta SD, o **una URL que consulta con una programación**; si la descarga falla, mantiene la imagen y reintenta con espera de 5 min hasta 6 h |
| Energía | Sueño profundo por defecto, ~10 µA y meses de batería (afirmado); modo siempre encendido ~40–80 mA |
| Wifi | `wifi.txt` en la SD, portal cautivo o app acompañante |
| Otros | Interfaz web con subida de imágenes, API REST con contraseña opcional, estado de batería, CLI en Node.js para procesar imágenes |
| Entorno | ESP-IDF (CMake, `idf.py`) con un `build.py` por placa |
| Madurez | 620 commits, 260 estrellas, 60 forks, 15 incidencias abiertas; sin fechas de versiones visibles |

**Encaje con el diseño:** coincide casi punto por punto con D5 y D33 (dormir y consultar una URL cada X tiempo). El servidor solo tendría que servir una imagen ya reducida a los seis colores. Falta verificar si admite autenticación con un secreto de cabecera compatible con D22, y en qué estado está su mantenimiento.

## Waveshare ESP32-S3-PhotoPainter (código de serie)

- Repositorio: [wurongmin/ESP32-S3-PhotoPainter](https://github.com/wurongmin/ESP32-S3-PhotoPainter), con un zip de código de ejemplo y esquema ([wiki](https://www.waveshare.com/wiki/ESP32-S3-PhotoPainter)).
- Sin soporte técnico oficial.
- Incluye un convertidor de imágenes para Windows y Mac para el panel de seis colores.

## Ejemplo ESP-IDF en el registro de Espressif

[esp32s3_photopainter](https://components.espressif.com/components/tuanpmt/esp_epaper/versions/2.0.0/examples/esp32s3_photopainter) (componente `tuanpmt/esp_epaper`): panel GDEP073E01 (800×480, 6 colores), mapeo de pines, LVGL 9 y tramado Floyd–Steinberg. Se construye con `idf.py set-target esp32s3`.

## Otros

- **ESPHome, cookbook EE0x:** [wiki de Seeed](https://wiki.seeedstudio.com/EE04_with_esphome_advanced/). Ejemplos con ESP-IDF o Arduino para la EE04, con pantalla de 2,9"; los tipos de pantalla listados no incluían el Spectra 6 de 7,3".
- **SenseCraft HMI / Seeedash:** plataforma sin código de Seeed; no es abierta en el sentido de D7/D8 salvo que se compruebe.
- **TRMNL:** firmware soportado por las reTerminal, pero requiere licencia de pago para ejecutarlo en hardware propio, según Switch Science. Descartado por D8.
