# Guía de compra: placa ESP32 con pantalla de 7,0"

Consultado: 2026-10-20. Apoya la decisión A5 de [decisiones-de-diseno.md](../decisiones-de-diseno.md). Esta guía describe opciones y trade-offs; **no decide**.

| Fichero | Contenido |
|---|---|
| [00-requisitos.md](00-requisitos.md) | Dimensionamiento: cuánta memoria, flash y procesador necesita el programa |
| [01-modelos.md](01-modelos.md) | Seis modelos de 7": especificaciones, hojas de datos, encaje con el proyecto, problemas de rendimiento |
| [02-vendedores.md](02-vendedores.md) | Ofertas por tienda: precio, país de envío, stock, y coste total estimado en España |
| [03-envio-y-aduanas.md](03-envio-y-aduanas.md) | IVA, derecho aduanero de 3 € y plazos por país de origen |

## Lo que debes saber antes de comprar

1. **Los ~50 € para dos placas de 7" no se alcanzan hoy con producto nuevo.** Los precios de referencia de las guías (~US$21–28 la unidad) no aparecen en las tiendas consultadas: en AliExpress hoy los anuncios del 8048S070C van de 40 € a 76 €. Entregadas en España, dos placas cuestan **~75–120 €** (detalle y fórmula en [02-vendedores.md](02-vendedores.md)).
2. **Desde el 1 de julio de 2026 hay un derecho de 3 € por código arancelario** en importaciones de hasta 150 € (un solo derecho por las dos placas iguales en un pedido), además del IVA. Quien compre fuera de la UE paga más de lo que muestra el anuncio.
3. **Los datos de envío casi nunca salen en la ficha.** Hay que llegar al carrito para ver plazo, coste e impuestos.
4. **La misma placa tiene revisiones distintas** (v0.1, v1.1, v1.3, v1.4) con código de inicio y táctil distintos. Confirmar sufijo **C** (capacitivo) y revisión con el vendedor.

## Modelos de un vistazo

| Modelo | Pantalla | Precio de referencia | Encaje | Riesgo principal |
|---|---|---|---|---|
| A. Sunton/Jingcai 8048S070C | 800×480 | ~37–53 € por unidad (UE), 40–76 € (AliExpress) | Encaja con ajustes | Revisiones distintas; pin INT del táctil sin conectar; listados con N4R8 y panel TN |
| B. Waveshare ESP32-S3-Touch-LCD-7 | 800×480 | US$32–37 (oficial) | Encaja, mejor documentación | Jitter si hay escrituras en flash (ver modelos) |
| C. Elecrow CrowPanel Advance 7" | 800×480 | 49,95 € (agotada) | Encaja; sobran funciones | Poco stock en la UE |
| D. Guition JC1060P470C (P4) | 1024×600 | US$34,9 (descatalogada en SpotPear) | Sobra potencia | Software inmaduro, wifi vía coprocesador |
| E. Waveshare 7B | 1024×600 | 65,84 € | Fuera de presupuesto | Precio |
| F. Makerfabs MaTouch 7" | 800×480 / 1024×600 | 59,95 € (Elektor) | Fuera de presupuesto | Precio; sin hoja de datos enlazada |

## Opciones por escenario (sin decidir por ti)

| Escenario | Qué encaja | Trade-offs |
|---|---|---|
| **Gastar lo mínimo** | **A**, en Tinytronics V0.1 (37,25 € con IVA, 50+ en stock en NL, sin aduana) o el anuncio mejor valorado de AliExpress | Hay que aceptar la revisión V0.1 (otro chip de audio) o revisión desconocida; trabajo extra en el pin INT del táctil |
| **Menos fricción de software** | **B**, en Waveshare oficial o Grobotronics | Más caro; Waveshare envía desde China con IVA y derecho al llegar |
| **Máxima calidad táctil potencial** | **D** | Mayor riesgo y esfuerzo; descatalogada; wifi vía coprocesador. *Hipótesis mía: la potencia extra da más margen para suavidad, sin medir* |

## Si hay que acercarse a ~50 € en total

Con los datos de hoy no se logra con producto nuevo de 7". Opciones que no he verificado y conviene investigar si interesa:
- Esperar a ofertas de AliExpress (suelen rebajar en el 11.11 *(conocimiento general, sin verificar)*).
- Buscar segunda mano.
- Comprar solo una placa para empezar a medir la latencia táctil real (A2) y decidir la segunda después.

## Antes de pulsar comprar

- [ ] Sufijo **C** (capacitivo) en el título.
- [ ] Revisión, flash (16 MB) y tipo de panel (IPS, no TN) confirmados por escrito con el vendedor.
- [ ] Coste de envío, plazo e impuestos vistos en el carrito.
- [ ] Vendedor con IOSS o almacén en la UE.
- [ ] Anotada la revisión recibida; documentarla en `docs/documentacion-tecnica.md`.
