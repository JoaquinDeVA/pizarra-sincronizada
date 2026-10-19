# Guía de compra — resumen

Actualizado: 2026-10-25. Este fichero es el resumen; los demás son el registro de datos. Apoya A5 (modelo de hardware) en [decisiones](../decisiones-de-diseno.md). Describe opciones; **no decide**.

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
- **Para "la mejor pantalla", la tecnología es Spectra 6:** es el color e-ink más cercano al papel con placas ESP32 disponibles. Gallery 3 y Kaleido 3 tienen peor color o blancos más oscuros, y no encontré placas ESP32 con ellos.
- **El precio real de la E1002 supera su MSRP de US$99:** RobotShop la vende a US$127,25 y Pakronics a A$193,60; una oferta de AliExpress de agosto de 2026 la daba a ~US$91 y no sé si sigue.
- **La E1004 de 13,3" cuesta 304,45 € en Openelab** (preventa, reposición estimada el 30-nov-2026, envío y aduana incluidos en la UE): es el "mejor posible" en tamaño y nitidez, pero ~3× el precio.
- **No pude consultar Amazon.es ni eBay** desde aquí.

## Opciones

| Opción | Pantalla | Batería | Caja | Apertura | Total en España *(estimación)* | Riesgo principal |
|---|---|---|---|---|---|---|
| **A. Waveshare PhotoPainter** | 7,3" 6 colores | Opcional | Marco de madera | ESP-IDF, GitHub, esquema; sin soporte | ~100–120 € | Marco "terminado": sin verificar que el USB-C quede accesible; precio con la batería sin confirmar |
| **B. Seeed XIAO EE04 (DIY Kit)** | 7,3" 6 colores | **No incluida** (conector) | **No** | Kit de desarrollo; soporte de terceros | ~82–88 € | Preventa; sin ejemplo oficial para el panel de 7,3"; hay que comprar batería y hacer la caja |
| **C. Seeed reTerminal E1002** | 7,3" 6 colores | 2000 mAh | Caja y soporte | Arduino, ESP-IDF, PlatformIO, ESPHome | ~115–150 € | Supera ~100 €; no encontré tienda de la UE con precio |
| **D. Seeed reTerminal E1004** | 13,3" 6 colores, 1200×1600 | 5000 mAh | Caja y soporte (376×311 mm) | Igual que la C | ~304 € (Openelab) | Preventa hasta finales de noviembre; tamaño grande |

## Qué decidir (A5)

- **Ajustarse a ~100 €:** la B es la única que entra, con batería y caja por tu cuenta (D12 ya contempla la caja impresa en 3D).
- **Menos trabajo:** la A trae marco, batería opcional y ejemplos, pero toca el límite y su "marco terminado" es la duda que planteaste.
- **Más acabado:** la C trae todo y es abierta, pero se pasa de presupuesto.
- **La mejor calidad con ESP32 abierto:** la D (13,3"), si el tamaño y ~304 € encajan.
- La A, la B y la C comparten el firmware `esp32-photoframe`, aunque por D18 el software va después.
