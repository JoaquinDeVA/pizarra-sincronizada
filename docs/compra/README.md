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
| Placa | Seeed XIAO ePaper Display Board **EE04** (SKU 100075670), 81×44×7 mm | **8,51 € en Mouser** · 11,50–12,83 € con IVA en Openelab |
| Panel | Seeed 7,3" **Spectra 6**, 800×480 (SKU 100064541), 170,2×111,2×0,9 mm | **38,70 € en Mouser** · 58,14 € con IVA en Openelab |
| Alternativa | **Kit DIY** de Openelab (placa + panel + antena) | 67,15 € (otro resultado: 70,35 €), preventa |
| Batería | LiPo 3,7 V con conector JST 2,0 mm | 5–10 € *(estimación)* |
| Caja | Impresa en 3D (D12) | 1–3 € de filamento *(estimación)* |

**Placa + panel en Mouser: 47,21 €** (8,51 + 38,70), unos 23 € menos que en Openelab. Con batería (5–10 €) y filamento (1–3 €) *(estimaciones)* salen **~55–60 €**, más el envío y el IVA si los precios no lo llevan (con IVA del 21 %: ~65–70 €). Ambas piezas están **sin stock** en Mouser ("Non-Stocked", plazo "solicitar presupuesto"). Falta confirmar en el carrito: IVA, envío, plazo y antena.

## Lo que falta para completar el producto

1. **Batería:** LiPo de 3,7 V, conector JST 2,0 mm, con protección. Seeed anuncia ~3 meses con la configuración por defecto sin decir la capacidad. Comprobar la polaridad con la placa (un comprador la recibió invertida).
2. **Antena:** el kit DIY incluye una antena FPC de 2,4 GHz; la ficha de la placa suelta no la lista. Confirmarlo.
3. **Caja:** diseño 3D propio. Hay modelo 3D de la placa y esquema, pero no dimensiones, agujeros de montaje ni medidas del panel.
4. **Cable USB-C con datos** y cargador (ya los tienes).
5. **Firmware y servidor:** vienen después (D18); hay base abierta (Seeed GFX2, `esp32-photoframe`).

## La realidad

- **Spectra 6, 7,3", color y ESP32, por menos de 100 €:** solo esta combinación. La PhotoPainter (~100–120 €) y la E1002 (~115–150 €) cuestan más; la E1004 y la EE02 de 13,3" son ~220–304 €.
- **El color es de 6 tintas** (negro, blanco, rojo, amarillo, verde y azul), con tramado. Refresco de 12–25 s sin parcial; ≥180 s entre refrescos como mínimo recomendado.
- **No hay ejemplo de 7,3" en la ruta que cita la wiki de la EE04** (sí de 7,5" monocromo). `esp32-photoframe` soporta la EE04.
- **Antes de comprar:** precio con IVA, envío, plazo y antena incluida, en el carrito de Mouser.
- **Mouser solo se deja leer con el navegador integrado;** las herramientas normales reciben "acceso denegado". **No pude consultar Amazon.es ni eBay.**

## Alternativas descartadas por precio o diseño (detalle en [02-modelos.md](02-modelos.md))

PhotoPainter (Waveshare), reTerminal E1002 y E1004 (Seeed), XIAO EE02 de 13,3", Inkplate 6COLOR, M5Stack PaperColor (4"), Pimoroni Inky Frame (no es ESP32), tricolor y monocromos (no cumplen D32).
