# Guía de compra — resumen

Actualizado: 2026-10-28. Solo hardware. Este fichero es el resumen; los demás son el registro de datos. Hardware elegido en D38 ([decisiones](../decisiones-de-diseno.md)); el software está en [software-existente.md](../software-existente.md) y la [documentación técnica](../documentacion-tecnica.md).

| Registro | Contenido |
|---|---|
| [01-requisitos.md](01-requisitos.md) | Qué necesita el cuadro: pantalla, energía, wifi, memoria |
| [02-modelos.md](02-modelos.md) | Fichas de la placa y el panel elegidos y de las alternativas |
| [03-vendedores.md](03-vendedores.md) | Ofertas, coste total en España, impuestos y envío |

## Qué se compra (D38)

| Pieza | Producto | Precio |
|---|---|---|
| Placa | Seeed XIAO ePaper Display Board **EE04** (SKU 100075670), 81×44×7 mm | **8,51 € sin IVA en Mouser** · 11,50–12,83 € con IVA en Openelab |
| Panel | Seeed 7,3" **Spectra 6**, 800×480 (SKU 100064541), 170,2×111,2×0,9 mm, zona activa 160×96 mm | **38,70 € sin IVA en Mouser** · 58,14 € con IVA en Openelab |
| Alternativa | **Kit DIY** de Openelab (placa + panel) | 67,15 € con IVA (otro resultado: 70,35 €), preventa |

**Incluido en la placa:** antena FPC de 2,4 GHz (2,9 dBi) y adaptadores con cables FFC de 24 y 50 pines. **Incluido en el panel:** adaptador y cable FFC de 50 pines. No hay que comprar nada más para conectarlos.

**Mouser:** placa + panel = 47,21 € sin IVA, **~57 € con el 21 %**, unos 13 € menos que Openelab con IVA. Ambas piezas están sin stock (plazo "solicitar presupuesto de entrega"). Falta ver el envío en el carrito.

## Se compra aparte

| Pieza | Qué | Precio estimado |
|---|---|---|
| Batería | LiPo de **1S (3,7 V)** con protección y conector JST-PH 2,0 mm, de dron o similar (D39). Las de dron suelen ser de 2S o más (7,4 V+) y con XT60: no valen así. Mouser no las vende en España | ~8–19 € *(estimación)*; se resuelve más adelante |
| Caja | Impresa en 3D (D12); hay medidas, no agujeros de montaje | 1–3 € de filamento *(estimación)* |
| Cable USB-C con datos y cargador | Ya los tienes | — |

**Total estimado: ~66–79 €** más envíos (Mouser y la batería irán por separado), dentro de los ~100 € de D6.

## La realidad

- **Spectra 6, 7,3", color y ESP32, por menos de 100 €:** solo esta combinación. La PhotoPainter (~100–120 €) y la E1002 (~115–150 €) cuestan más; la E1004 y la EE02 de 13,3" son ~220–304 €.
- **El color es de 6 tintas** (negro, blanco, rojo, amarillo, verde y azul). Refresco de 12–25 s sin parcial.
- **Cuidado del panel (Seeed):** refrescar al menos una vez al día y no dejar la misma imagen más de un día.
- **Los precios de Mouser no llevan IVA.**
- **Mouser solo se deja leer con el navegador integrado;** las herramientas normales reciben "acceso denegado". No pude consultar Amazon.es ni eBay.

## Alternativas descartadas por precio o diseño (detalle en [02-modelos.md](02-modelos.md))

PhotoPainter (Waveshare), reTerminal E1002 y E1004 (Seeed), XIAO EE02 de 13,3", Inkplate 6COLOR, M5Stack PaperColor (4"), Pimoroni Inky Frame (no es ESP32), tricolor y monocromos (no cumplen D32).
