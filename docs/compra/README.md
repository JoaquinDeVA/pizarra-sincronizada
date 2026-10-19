# Guía de compra — resumen

Actualizado: 2026-10-27. Este fichero es el resumen; los demás son el registro de datos. Hardware elegido en D38 ([decisiones](../decisiones-de-diseno.md)); queda abierta la tienda y las piezas (A5).

| Registro | Contenido |
|---|---|
| [01-requisitos.md](01-requisitos.md) | Qué necesita el cuadro: pantalla, energía, wifi sin táctil, memoria |
| [02-modelos.md](02-modelos.md) | Fichas de la placa elegida y de las alternativas |
| [03-vendedores.md](03-vendedores.md) | Ofertas, coste total en España, impuestos y envío |
| [04-software-existente.md](04-software-existente.md) | Firmware abierto que se puede reutilizar |

## Qué se compra (D38)

| Pieza | Producto | Precio de referencia |
|---|---|---|
| Placa | Seeed XIAO ePaper Display Board **EE04** (SKU 100075670), 81×44×7 mm; **incluye antena y cables FFC** | **8,51 € sin IVA en Mouser** · 11,50–12,83 € con IVA en Openelab |
| Panel | Seeed 7,3" **Spectra 6**, 800×480 (SKU 100064541), 170,2×111,2×0,9 mm, zona activa 160×96 mm | **38,70 € sin IVA en Mouser** · 58,14 € con IVA en Openelab |
| Alternativa | **Kit DIY** de Openelab (placa + panel + antena) | 67,15 € (otro resultado: 70,35 €), preventa |
| Batería | LiPo 3,7 V, JST-PH 2,0 mm, con protección. **Mouser no la vende en España** | ~8–19 € según tienda (ver [03-vendedores.md](03-vendedores.md)) |
| Caja | Impresa en 3D (D12) | 1–3 € de filamento *(estimación)* |

**Placa + panel en Mouser: 47,21 € sin IVA, ~57 € con el 21 %** (8,51 + 38,70), unos 13 € menos que en Openelab con IVA. Con batería (~8–19 €) y filamento (1–3 € *(estimación)*) salen **~66–79 €**, más envíos (Mouser y la batería irán por separado). Ambas piezas están **sin stock** en Mouser ("Non-Stocked", plazo "solicitar presupuesto"). Falta confirmar en el carrito: envío y plazo.

## Lo que falta para completar el producto

1. **Batería:** LiPo de 3,7 V, conector JST 2,0 mm, con protección. Seeed anuncia ~3 meses con la configuración por defecto sin decir la capacidad. Comprobar la polaridad con la placa (un comprador la recibió invertida).
2. **Antena: resuelta.** La hoja de datos de la placa la incluye (FPC de 2,4 GHz, 2,9 dBi), con los adaptadores y cables FFC de 24 y 50 pines. El panel trae otro adaptador y cable de 50 pines.
3. **Caja:** diseño 3D propio. Hay modelo 3D de la placa (GrabCAD), esquema y medidas (placa 81×44×7 mm, panel 170,2×111,2×0,9 mm, zona activa 160×96 mm); faltan los agujeros de montaje. No hay caja ya hecha para esta combinación; el modelo más cercano es un [marco de MakerWorld para el panel de Waveshare de 7,3"](https://makerworld.com/models/1924771), con el mismo contorno de 170,2×111,2 mm, pensado para otra placa.
4. **Cable USB-C con datos** y cargador (ya los tienes).
5. **Firmware y servidor:** vienen después (D18); hay base abierta (Seeed GFX2, `esp32-photoframe`).
6. **Cuidado del panel:** refrescar al menos una vez al día y no dejar la misma imagen más de un día (datasheet de Seeed).

## La realidad

- **Spectra 6, 7,3", color y ESP32, por menos de 100 €:** solo esta combinación. La PhotoPainter (~100–120 €) y la E1002 (~115–150 €) cuestan más; la E1004 y la EE02 de 13,3" son ~220–304 €.
- **El color es de 6 tintas** (negro, blanco, rojo, amarillo, verde y azul), con tramado. Refresco de 12–25 s sin parcial; ≥180 s entre refrescos como mínimo recomendado.
- **No hay ejemplo de 7,3" en la ruta que cita la wiki de la EE04** (sí de 7,5" monocromo). `esp32-photoframe` soporta la EE04.
- **Antes de comprar:** envío y plazo en el carrito de Mouser; los precios de Mouser **no llevan IVA**.
- **Mouser solo se deja leer con el navegador integrado;** las herramientas normales reciben "acceso denegado". **No pude consultar Amazon.es ni eBay.**

## Alternativas descartadas por precio o diseño (detalle en [02-modelos.md](02-modelos.md))

PhotoPainter (Waveshare), reTerminal E1002 y E1004 (Seeed), XIAO EE02 de 13,3", Inkplate 6COLOR, M5Stack PaperColor (4"), Pimoroni Inky Frame (no es ESP32), tricolor y monocromos (no cumplen D32).
