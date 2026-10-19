# Guía de compra — resumen

Actualizado: 2026-10-22. Este fichero es el resumen; los demás son el registro de datos. Apoya A5 (tecnología de pantalla) y la elección de modelo ([decisiones](../decisiones-de-diseno.md)).

| Registro | Contenido |
|---|---|
| [00-requisitos.md](00-requisitos.md) | Cuánta memoria, flash y procesador necesita el programa |
| [01-modelos.md](01-modelos.md) | Fichas de placas LCD de 7": especificaciones, hojas de datos, problemas |
| [02-vendedores.md](02-vendedores.md) | Ofertas por tienda, coste total en España, impuestos y envío, lista de comprobación |
| [03-tecnologias-de-pantalla.md](03-tecnologias-de-pantalla.md) | LCD frente a e-paper y otras, con consumo y refresco |

## La realidad

- **Precio:** dos placas LCD de 7" nuevas cuestan **~75–120 € entregadas en España**, no los ~50 € del objetivo. Las guías citan ~US$21–28 por unidad; hoy no aparecen en las tiendas consultadas.
- **Impuestos:** desde el 1-jul-2026 hay un derecho de 3 € por código arancelario, además del IVA, en importaciones de fuera de la UE.
- **Memoria:** no limita. Bastan ~2 MB de PSRAM y las placas traen 8 MB.
- **Consumo de la LCD:** 2,25 W ≈ 3–5 €/año por aparato encendido 24 h *(estimación)*. No es un problema de coste.
- **Brillo:** una LCD encendida llama la atención. Se mitiga con brillo bajo y apagado por inactividad (D25); el fondo negro no apaga un panel IPS.
- **e-paper:** sin brillo y casi sin consumo, pero cada actualización parcial tarda 0,4–1,5 s y deja imagen residual. Choca con D15 (calidad táctil), y no hay panel de 7" con táctil verificado (los táctiles son de 4,7" y 6"). Su ventaja de batería no cuenta con D5 (USB-C constante).
- **eBay y Amazon** no se pueden consultar desde aquí; hay búsquedas a mano en [02](02-vendedores.md).

## Opciones

| Opción | Pantalla | Precio de referencia | Cambia | Principal riesgo |
|---|---|---|---|---|
| **A. Waveshare ESP32-S3-Touch-LCD-7** (D17 actual) | LCD 7" 800×480 | ~28–32 € por unidad *(sin verificar)* · ~80–107 € las dos entregadas | Nada | Precio sobre el objetivo; jitter si se escribe en flash |
| **B. Sunton/Jingcai 8048S070C** | LCD 7" 800×480 | 37,25 € (Tinytronics V0.1) · 40–76 € (AliExpress) | Nada | Revisiones distintas; INT del táctil sin conectar (puente en R17) |
| **C. LilyGo T5-4.7-S3** | e-paper 4,7" 960×540 | 35–45 US$ · 55 € (Openelab) | **D10** (7") y **D15** | Latencia por refresco; imagen residual; agotada en LilyGo |
| **D. LCD con brillo mitigado** | LCD 7" (A o B) | igual | Nada, solo configuración | Sigue siendo una pantalla luminosa |

Detalle en [01-modelos.md](01-modelos.md) y [03-tecnologias-de-pantalla.md](03-tecnologias-de-pantalla.md).

## Qué decidir (A5)

Si el brillo y el consumo pesan más que la calidad táctil y los 7", el camino es e-paper (opción C) y habría que cambiar D10 y D15. Si no, se mantiene LCD 7" con brillo bajo y apagado automático (opción D sobre la A o la B). Hoy los datos no sostienen un e-paper de 7" táctil.
