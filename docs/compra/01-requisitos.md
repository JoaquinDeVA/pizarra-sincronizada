# Requisitos del cuadro (registro)

Consultado: 2026-10-24. Resumen en [README.md](README.md). Derivados de las decisiones D1–D33. *(estimación)* = mía.

## Requisitos de hardware

| Requisito | Valor | Decisión |
|---|---|---|
| Pantalla | e-ink a color, sin táctil, ≥5" | D30, D32 |
| Procesador | ESP32 con wifi | D7 |
| Energía | Conector de batería con cargador integrado; sueño profundo con despertar por temporizador (RTC) | D5 |
| Entrada de datos | Wifi: consulta cada X tiempo a un servidor propio | D33 |
| Programación | Puerto USB-C de programación, flasheo documentado, firmware y esquema abiertos | D7, D8 |
| Wifi sin táctil | Algún medio físico o de radio para dar la red: botones, tarjeta SD o Bluetooth | D4, A1 |
| Caja | Marco o soporte incluido, o espacio para una caja impresa en 3D | D12 |
| Precio | Hasta ~100 € entregado en España | D6 |

## Pantalla e-ink a color (Spectra 6)

- Seis tintas: negro, blanco, rojo, amarillo, verde y azul. Las imágenes de color se aproximan con tramado (*dithering*); una app de dibujo solo muestra esos seis colores.
- Resolución típica a 7,3": 800×480 (≈ 165×99 mm, calculado con píxeles cuadrados).
- **Sin refresco parcial.** Refresco completo: 12 s en la ficha de Waveshare, 25 s en su manual, 15–20 s en Seeed ([Waveshare 7,3" HAT (E)](https://www.waveshare.com/wiki/7.3inch_e-Paper_HAT_%28E%29_Manual), [Seeed](https://wiki.seeedstudio.com/reterminal_e10xx_main_page/)).
- Recomendaciones del manual de Waveshare: ≥180 s entre refrescos y uno completo al menos cada 24 h. Potencia al refrescar <70 mW típica; en reposo mantiene la imagen sin alimentación.

## Cuidados del panel (Seeed)

- Refrescar **al menos una vez al día**, aunque la imagen no cambie; no dejar la misma imagen más de un día (retención de imagen).
- Limpiar la pantalla si lleva mucho tiempo parada.
- Evitar refrescos rápidos y prolongados: acortan la vida del panel.
- Fuente: [datasheet del panel](https://www.mouser.es/datasheet/3/1361/1/100064541.pdf). El manual de Waveshare para otro panel Spectra 6 da la misma pauta de uno cada 24 h como mínimo.

## Memoria

| Concepto | Tamaño | Nota |
|---|---|---|
| Imagen 800×480 a 4 bits (6 colores) | 192 KB | cálculo |
| Imagen 800×480 en RGB565 | 750 KB | cálculo |
| Imagen 800×480 en RGB888 | 1,15 MB | cálculo |
| PNG/BMP descargado y decodificado | del orden de los anteriores | las placas candidatas traen 8 MB de PSRAM |

El servidor puede entregar la imagen ya reducida a los colores del panel para que el cuadro solo la descargue y la pinte (A8 del diseño: cómo se compone la imagen). Así la memoria deja de ser un límite.

## Energía y autonomía

| Dato | Valor | Fuente |
|---|---|---|
| Sueño del firmware existente | ~10 µA (afirmado); meses de batería | [esp32-photoframe](https://github.com/aitjcize/esp32-photoframe) |
| Sueño según la wiki de Waveshare (PhotoPainter) | ≤1 mA | [Wiki](https://www.waveshare.com/wiki/ESP32-S3-PhotoPainter) |
| Siempre encendido (wifi conectado) | ~40–80 mA | esp32-photoframe |
| Reposo de la pantalla | <0,01 µA | [Waveshare](https://www.waveshare.com/7.3inch-e-paper-hat-e.htm?sku=27863) |
| Seeed reTerminal E1002 | ~3 meses con batería de 2000 mAh a un refresco cada 6 h | [Seeed](https://wiki.seeedstudio.com/reterminal_e10xx_main_page/) |

- Los 10 µA y el ≤1 mA se contradicen por 100×: dormir a 1 mA gasta una batería de 1500 mAh en unos 62 días sin contar los despertares. **Hay que medir el consumo real de la placa elegida.**
- **Requisito (D35):** una carga a la semana. Con una batería de 1500 mAh son ≤214 mAh/día. Dormir a 1 mA gasta 24 mAh/día; quedan ~190 mAh/día para despertares.
- Coste por despertar *(estimación mía, sin medir)*: ~0,14 mAh si solo consulta (wifi + petición) y ~0,45 mAh si además refresca la pantalla. Con ~190 mAh/día caben ~1.350 consultas sin refresco (una cada ~64 s) o ~420 con refresco (una cada ~3,4 min). Con el mínimo de D34 (3 min) y refresco solo si cambia la imagen, **una semana es viable incluso con el sueño de 1 mA**.

## Wifi sin táctil: lo que ofrece cada vía

| Vía | Qué hace falta | Visto en |
|---|---|---|
| Portal cautivo | El cuadro crea su propia red; se abre un menú en el navegador del móvil, normalmente en 192.168.4.1 | esp32-photoframe; firmware de serie de PhotoPainter |
| Fichero `wifi.txt` en tarjeta SD | Ranura microSD | esp32-photoframe |
| Bluetooth | Provisión cifrada de Espressif (*Unified Provisioning*) y una app o web con BLE | [Espressif](https://docs.espressif.com/projects/esp-idf/en/v5.2.1/esp32s3/api-reference/provisioning/provisioning.html) |
| Botones | Botones de la placa para elegir entre redes guardadas | EE04 y E1002: 3 botones de usuario |

## Qué se mira en un producto para saber si es abierto

Puerto USB-C accesible para programar, modo de descarga documentado, esquema publicado, repositorio con código, ejemplos con ESP-IDF o Arduino y soporte de firmware de terceros.
