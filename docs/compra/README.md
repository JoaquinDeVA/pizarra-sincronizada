# Guía de compra — resumen

Actualizado: 2026-10-24. Este fichero es el resumen; los demás son el registro de datos. Apoya A5 (modelo de hardware) en [decisiones](../decisiones-de-diseno.md). Describe opciones; **no decide**.

| Registro | Contenido |
|---|---|
| [01-requisitos.md](01-requisitos.md) | Qué necesita el cuadro: pantalla, energía, wifi sin táctil, memoria |
| [02-modelos.md](02-modelos.md) | Fichas de los modelos de e-ink a color, con la apertura de cada uno |
| [03-vendedores.md](03-vendedores.md) | Ofertas, coste total en España, impuestos y envío |
| [04-software-existente.md](04-software-existente.md) | Firmware abierto que se puede reutilizar |

## La realidad

- **Encontré 3 kits de e-ink de color de 7,3" que cumplen** ESP32, batería y acceso de desarrollo: PhotoPainter, XIAO EE04 y reTerminal E1002. Los de color con otro tamaño que vi son más caros (13,3" a US$279,90; 5,8" a 209,95 €) o no son ESP32. No he recorrido todo el mercado.
- **Hay firmware abierto casi hecho.** `esp32-photoframe` (MIT) soporta las tres, duerme entre consultas y descarga una imagen de una URL con una programación. Es justo el modelo de D5 y D33. Falta comprobar su mantenimiento y si admite la autenticación de D22.
- **El color es de 6 tintas** (negro, blanco, rojo, amarillo, verde y azul). Las imágenes salen tramadas y una app de dibujo solo muestra esos seis colores.
- **Refresco de 12–25 s sin parcial,** con un mínimo recomendado de ≥180 s entre refrescos.
- **Dos datos de autonomía se contradicen:** el firmware dice ~10 µA durmiendo y la wiki de Waveshare, ≤1 mA. Hay que medirlo.
- **Presupuesto (D6, ~100 €):** solo la EE04 entra claramente (~82–88 €). La PhotoPainter roza (~100–120 €) y la E1002 se pasa (~115–140 €). Estimaciones mías con conversión de dólares sin verificar.
- **No pude consultar Amazon.es ni eBay** desde aquí.

## Opciones

| Opción | Pantalla | Batería | Caja | Apertura | Total en España *(estimación)* | Riesgo principal |
|---|---|---|---|---|---|---|
| **A. Waveshare PhotoPainter** | 7,3" 6 colores | Opcional | Marco de madera | ESP-IDF, GitHub, esquema; sin soporte | ~100–120 € | Marco "terminado": sin verificar que el USB-C quede accesible; precio con la batería sin confirmar |
| **B. Seeed XIAO EE04 (DIY Kit)** | 7,3" 6 colores | **No incluida** (conector) | **No** | Kit de desarrollo; soporte de terceros | ~82–88 € | Preventa; sin ejemplo oficial para el panel de 7,3"; hay que comprar batería y hacer la caja |
| **C. Seeed reTerminal E1002** | 7,3" 6 colores | 2000 mAh | Caja y soporte | Arduino, ESP-IDF, PlatformIO, ESPHome | ~115–140 € | Supera el presupuesto |

## Qué decidir (A5)

- **Ajustarse a ~100 €:** la B es la única que entra, con batería y caja por tu cuenta (D12 ya contempla la caja impresa en 3D).
- **Menos trabajo:** la A trae marco, batería opcional y ejemplos, pero toca el límite y su "marco terminado" es la duda que planteaste.
- **Más acabado:** la C trae todo y es abierta, pero se pasa de presupuesto.
- Sea cual sea, las tres comparten el firmware `esp32-photoframe`, así que el software no depende de la elección.
