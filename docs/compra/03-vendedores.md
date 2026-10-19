# Vendedores, impuestos y coste total (registro)

Consultado: 2026-10-24. Resumen en [README.md](README.md). Modelos en [02-modelos.md](02-modelos.md).

Entrega en España peninsular. "Visto" = ficha abierta; "sin verificar" = fragmento de búsqueda. *(estimación)* = mía. Conversión: 1 US$ ≈ 0,87–0,92 € *(estimación, sin cotización verificada)*.

## Ofertas

| Modelo | Vendedor | País / origen del envío | Precio | Estado | Impuestos y envío | Fiabilidad |
|---|---|---|---|---|---|---|
| B. XIAO EE04 | [Openelab](https://openelab.io/products/seeed-studio-xiao-epaper-7-3-inch-diy-kit) | UE (almacén de Múnich o de Shenzhen según stock) | 67,15 € con IVA (la ficha muestra además 52,45 € como "regular": inconsistente) | **Preventa**; despacho 10–20 días tras pedir | Cubre aranceles e IVA (DDP) en la UE; FedEx 7,95 € por debajo de 100 €, gratis por encima, 3–5 días hábiles tras el despacho. Devolución de 14 días | Visto |
| A. PhotoPainter | [Waveshare](https://www.waveshare.com/esp32-s3-photopainter.htm) | China | US$82,99–83,99 | No visible | Envío, IVA y aduana solo en el carrito | Visto |
| A. PhotoPainter | [Little Bird (AU)](https://littlebirdelectronics.com.au/products/esp32-s3-photopainter-7-3-e-paper-display-wood-frame-800x480-optional-battery-ws-32408) | Australia | No consultado | — | No es UE | Sin verificar |
| C. E1002 | Seeed Studio (tienda oficial) | No consta | US$99 (MSRP según la wiki) | No consultado | No consultado | Wiki |
| E1001 (monocromo, referencia) | Switch Science | Japón | ¥14.425 | En stock | — | Búsqueda |

No pude consultar Amazon.es ni eBay (mis herramientas reciben error 403 y 503). Búsquedas a mano, sin verificar que coincidan: [eBay.es: PhotoPainter](https://www.ebay.es/sch/i.html?_nkw=ESP32-S3-PhotoPainter) · [Amazon.es: e-paper 7.3 Spectra 6 ESP32](https://www.amazon.es/s?k=ESP32+e-paper+7.3+Spectra+6).

## Coste total estimado de una unidad entregada en España

Fórmula: `precio + envío + IVA (21 %, si no incluido) + 3 € (derecho aduanero, solo importaciones de fuera de la UE)`.

| Opción | Cuentas | Total |
|---|---|---|
| **B. EE04 en Openelab** | 67,15 € + 7,95 € de envío = 75,10 €; + batería LiPo con conector JST 2,0 mm (~5–10 € *(estimación)*) + 1–3 € de filamento para la caja | **~82–88 €** |
| A. PhotoPainter en Waveshare | US$83 ≈ 72–77 € ; ×1,21 de IVA = 87–94 €; + 3 €; + envío 10–25 € *(estimación)* | **~100–120 €** (la variante con batería puede costar más; sin verificar) |
| C. E1002 en Seeed | US$99 ≈ 86–91 €; ×1,21 = 104–110 €; + 3 €; + envío | **~115–140 €** |

**Lectura:** la EE04 es la única que entra claramente en ~100 € (D6), pero es preventa, no trae batería ni caja, y no tiene ejemplo oficial para el panel de 7,3". La PhotoPainter roza el límite y trae marco y ejemplos; la E1002 lo supera.

## Impuestos y envío por país de origen

| Concepto | Dato | Fuente |
|---|---|---|
| IVA | Desde el 1-jul-2021 se cobra IVA a toda importación comercial, sin mínimo | [ShippyPro](https://www.shippypro.com/blog/es/ioss-y-oss-guía-del-iva-para-e-commerce-transfronterizo-en-2026) · [AEAT IOSS](https://www3.agenciatributaria.gob.es/static_files/Sede/Tema/Aduanas/Compra_internet_ecomm/FolletoIOSSgral.pdf) |
| IOSS | Hasta 150 €: si el vendedor está registrado, cobra el IVA al pagar; si no, se cobra al recibir, a veces con gastos del transportista | ídem |
| Derecho aduanero | Desde 1-jul-2026, 3 € por código arancelario (no por cantidad) en envíos de hasta 150 € | [Comisión Europea](https://commission.europa.eu/news-and-media/news/ensuring-fairness-and-safety-eur3-customs-duty-low-value-parcels-2026-06-29_en) |

| Origen | Plazo a España | Impuestos | Fuente |
|---|---|---|---|
| China | 5–15 días *(fuente secundaria)*; retenciones de semanas en algunos casos | IVA + 3 € | [Banda Ancha](https://bandaancha.eu/foros/pago-aduanas-1734972) · [Packlink](https://www.packlink.com/eu-ES/blog/china-post-servicios-envios-seguimiento/) |
| UE (Openelab, almacén de Múnich) | 3–5 días hábiles tras el despacho | IVA incluido; sin aduana | Ficha |
| Almacén de AliExpress en España | 3–7 días | Ya nacionalizado | [Alimarket](https://www.alimarket.es/logistica/noticia/324880/aliexpress-mejora-su-logistica-en-espana-para-acortar-los-tiempos-de-entrega) |

## Lista de comprobación antes de comprar

- Para la EE04: confirmar que la preventa sigue vigente, el precio en el carrito (la ficha es inconsistente), la compatibilidad con una batería y si el panel de 7,3" va incluido (la ficha dice que sí).
- Para la PhotoPainter: elegir variante con o sin batería, ver envío, IVA y aduana en el carrito, y revisar si el USB-C queda accesible con el marco.
- Guardar capturas del precio y de la revisión de la placa.
