# Documentación técnica — Pizarra sincronizada

Táctica: **cómo** se materializan las decisiones de [decisiones-de-diseno.md](decisiones-de-diseno.md) (se citan como D1, P1…). Aquí no se discute el porqué.

Fecha: 2026-10-16 · Estado: diseño, sin código

> Precios: capturas de octubre de 2026. Cambian a menudo; confirma antes de comprar. *(estimación)* = cifra mía, sin fuente.

---

## 1. Visión general

```
  Casa A                                   Casa B
 ┌────────────┐      wss (TLS)       ┌────────────┐
 │ ESP32 + LCD│◄────────────────────►│ ESP32 + LCD│
 │ LVGL canvas│        ▲     ▲       │ LVGL canvas│
 └────────────┘        │     │       └────────────┘
                   ┌───┴─────┴───┐
                   │   RELAY     │  sala = código; guarda el registro
                   │ (Node ~150  │  de trazos para quien se reconecta
                   │  líneas)    │
                   └─────────────┘
```

Tres piezas: el **aparato** (placa ESP32 con pantalla, ×2), el **relay** (servidor en internet) y la **caja** (impresa en 3D). El **lápiz** es opcional y aparte (D12).

---

## 2. Hardware (D6, D7, D8, D11)

Placas "todo en uno" de la familia Sunton/Guition. Se venden montadas: no hay que diseñar PCB.

### Candidato A: Guition JC4827W543C, 4,3"

| Dato | Valor |
|---|---|
| Pantalla | 4,3" IPS, 480×272 (NV3041A por QSPI) |
| Táctil | Capacitivo GT911 (variante **C**; R = resistiva, N = sin táctil) |
| SoC | ESP32-S3 (N4R8): 4 MB flash, 8 MB PSRAM |
| Conectividad | Wifi, BLE 5, USB-C, ranura SD |
| Batería | Sin conector (coherente con D5) |
| Precio | ~US$10–14 de calle; un anuncio genérico en eBay.de a 20,99 € sin confirmar modelo |

Pegas ([Atomic14](https://www.atomic14.com/esp32/boards/guition-jc4827w543/)): 4 MB de flash (firmware ajustado), solo rotaciones 0°/180°, requiere invertir colores y byte-swap, y en Arduino_GFX el táctil y la SD pueden fallar tras refrescar si no se cuida el orden.

### Candidato B: Sunton ESP32-8048S070C, 7"

| Dato | Valor |
|---|---|
| Pantalla | 7" IPS RGB, 800×480 |
| Táctil | Capacitivo GT911 |
| SoC | ESP32-S3: 16 MB flash, 8 MB PSRAM |
| Conectividad | Wifi, BLE 5, USB-C, ranura SD, altavoz |
| Batería | No tiene |
| Precio | ~US$21–28 de calle; UE: 37,25 € en Tinytronics (agotada) y 52,95 € en Hobbyelectronica |

Pegas ([Atomic14](https://www.atomic14.com/esp32/boards/sunton-esp32-8048s070/)): las revisiones v1.1 y v1.3 necesitan código de inicio distinto; el framebuffer está en PSRAM y puede parpadear con wifi si no se ajusta LVGL; ~2 GPIO libres. Alimentación: asume 5 V / 2 A y compruébalo.

### Comparativa

| | A: 4,3" | B: 7" |
|---|---|---|
| Calle (AliExpress/eBay) | ~10–14 € | ~21–28 € |
| Tienda UE | ~21 € (sin confirmar) | ~37–53 € |
| Resolución | 480×272 | 800×480 |
| Flash / PSRAM | 4 MB / 8 MB | 16 MB / 8 MB |
| Dentro de D6 | Sí, con margen | Roza o supera, según envío/IVA |

### Compra (D14)

- **AliExpress/eBay:** busca `JC4827W543C` o `ESP32-8048S070C`. Comprueba sufijo **C** y revisión; 2–4 semanas *(estimación)*; el precio no incluye envío ni IVA/aduanas.
- **Tienda UE:** más rápida y con garantía, ~2×. 7": [Tinytronics](https://www.tinytronics.nl/nl/development-boards/microcontroller-boards/met-wi-fi/jingcai-esp32-8048s070c-7-inch-tft-lcd-display-800*480-pixels-met-touchscreen-esp32-s3), [Hobbyelectronica](https://www.hobbyelectronica.nl/en/product-categorie/platformen/esp32-display/). Para 4,3" no he localizado una tienda UE fiable.
- Comparativa de 7": [Atomic14](https://www.atomic14.com/esp32/boards/7-inch-displays/).

### Presupuesto por aparato

| Pieza | A: 4,3" | B: 7" |
|---|---|---|
| Placa | ~10–14 € | ~21–28 € |
| Caja impresa en 3D | ~1–3 € de filamento *(estimación)* | igual |
| Cable USB-C y cargador | ya los tienes | igual |
| Lápiz (opcional) | ~1–5 € *(estimación)* | igual |
| **Total** | **~12–21 €** | **~24–36 €** |

---

## 3. Alimentación (D5, D3)

- Entrada USB-C a 5 V; recomendable cargador de ≥2 A.
- Coche: adaptador 12 V→USB-C. Fuera de casa: powerbank.
- Sin batería propia: no se necesita cargador LiPo ni comprobar polaridades.
- Sin datos del móvil no hay sincronización: el aparato no lleva 4G.

---

## 4. Conexión wifi (D2, D4, P4)

1. Primer arranque, o sin ninguna red conocida: la placa crea su propia red (portal cautivo). Se elige el wifi desde el móvil.
2. Se guardan varias redes (casa, oficina, hotspot).
3. Al encender intenta las conocidas en orden; el indicador de pantalla muestra el estado.
4. **No soporta** portales con login de hoteles.

---

## 5. Relay (P1, P2, P8)

Programa en Node.js con [`ws`](https://github.com/websockets/ws) (MIT), un solo archivo.

| Opción de alojamiento | Coste | Nota |
|---|---|---|
| PC/Raspberry en casa + túnel (p. ej. Cloudflare Tunnel) | ~0 € | Condiciones del túnel gratuito sin verificar aquí; requiere equipo encendido |
| VPS pequeño | [Hetzner CAX11](https://www.vpsbenchmarks.com/hosters/hetzner/plans/cax11) ≈ 5,99 €/mes más IPv4 *(subió en 2026)* | Lo más estable; vale cualquier VPS de 3–6 €/mes |
| Cloudflare Workers + Durable Objects (gratis) | 0 € hasta los [límites](https://developers.cloudflare.com/durable-objects/platform/pricing) | Te ata a su API |
| Oracle "Always Free" | 0 € | No usar: [recortó el tier en junio de 2026](https://linuxiac.com/oracle-quietly-cuts-free-tier-ampere-a1-resources-in-half/) |

El relay no usa nada propietario: puede moverse entre opciones sin reescribir.

---

## 6. Protocolo (P2, P3)

JSON por WebSocket (wss). Coordenadas en el lienzo lógico; el aparato escala a su pantalla.

```
Cliente → relay
  {"t":"join","room":"ABC123","key":"<secreto>"}
  {"t":"b","id":17,"c":"#e53935","w":6}        // empieza trazo (goma = color del fondo, P3)
  {"t":"p","id":17,"pts":[[x,y],[x,y],...]}   // puntos del trazo
  {"t":"e","id":17}                             // fin de trazo
  {"t":"clear"}                                 // borrar todo
Relay → cliente
  {"t":"snap","log":[...]}                      // al entrar: registro desde el último clear
  (reenvía b/p/e/clear del otro cliente en orden)
```

- Sala = código de 6 caracteres + clave secreta; sin cuentas.
- El relay numera los eventos y guarda el registro; quien se reconecta recibe `snap`.
- Límite de registro: ~50.000 puntos *(mi decisión)*.
- Cada aparato pinta en local al instante y luego envía; puntos agrupados a ~30 Hz.
- Si los dos aparatos tienen proporciones distintas, se usan bandas (por eso P9).

---

## 7. Firmware (D9, D8)

| Pieza | Elección | Licencia |
|---|---|---|
| Entorno | PlatformIO + Arduino-ESP32 | Apache 2.0 / LGPL 2.1 |
| Gráficos y táctil | [LVGL 9](https://lvgl.io) (canvas) | MIT |
| WebSocket | `esp_websocket_client` o [arduinoWebSockets](https://github.com/Links2004/arduinoWebSockets) | Apache 2.0 / LGPL 2.1 |
| Wifi | Portal cautivo propio o [WiFiManager](https://github.com/tzapu/WiFiManager) | MIT |
| TLS | mbedTLS incluido | Apache 2.0 |
| Driver táctil | [esp_lcd_touch_gt911](https://components.espressif.com/components/espressif/esp_lcd_touch_gt911/versions/1.2.1/readme) | Apache 2.0 |

Notas:
- Memoria A: framebuffer 480×272×2 B ≈ 255 KB en PSRAM; firmware en 4 MB de flash.
- Memoria B: 800×480×2 B ≈ 750 KB en PSRAM; ajustar bounce buffers de LVGL.
- Se pinta solo el segmento nuevo, no todo el lienzo.
- UI mínima (P5): ~8 colores, 3 grosores, goma, "borrar todo" con confirmación, indicador de conexión.
- Reconexión automática con espera creciente.
- Verificar la licencia de cualquier driver o ejemplo del fabricante antes de reutilizarlo.

---

## 8. Lápiz (D12)

- Los dos candidatos usan GT911 (capacitivo). Vale cualquier **lápiz capacitivo pasivo de punta fina** (disco o malla, ~1,5–2 mm). Los activos (Apple Pencil, Wacom/AES, S-Pen) no funcionan.
- Precio ~1–5 € *(estimación)*; sin modelo verificado. Busca "capacitive stylus fine tip disc".
- Pruebas: probar un lápiz antes de comprar varios; suavizar coordenadas con media móvil en el firmware.

---

## 9. Caja (D13, D3)

- Soporte inclinado con hueco para el USB-C y marco que sujete la placa.
- Diseño paramétrico (OpenSCAD o CadQuery) en el repo, ajustable a A o B, con los STL publicados.
- Pendiente: medidas exactas de placa y posición de tornillos/conectores del lote comprado (varían por revisión).

---

## 10. Plan por fases (P6)

1. **Relay + cliente web de prueba** (coste 0): dos pestañas dibujando; valida protocolo, reconexión y `clear`.
2. **Una placa A** (~12 €): táctil, lápiz y wifi. Decide si basta 4,3" o hace falta 7".
3. **Firmware v1:** dibujar y borrar en local, sincronizar, portal wifi.
4. **Caja impresa** y prueba con lápiz.
5. **Segunda unidad** y prueba real en dos casas y con hotspot.

---

## 11. Riesgos

| Riesgo | Mitigación |
|---|---|
| 4,3" demasiado pequeña o gruesa | Prototipar con una sola placa; plan B = 7" |
| 7" supera D6 tras envío/IVA | Calcular total con envío antes de pedir |
| Revisiones de placa con init distinto | Anotar revisión al comprar; probar una unidad |
| 4 MB de flash en la de 4,3" | Firmware ligero, sin ESPHome ni librerías pesadas |
| Parpadeo de la 7" con wifi | Ajustes de LVGL (bounce buffers) |
| Lápiz poco fiable | Probar uno antes; suavizado de coordenadas |
| Portales de login de hoteles | No soportados; hotspot del móvil |
| Cambian precios o stock | Dos fuentes de compra; confirmar antes de pedir |

---

## Fuentes (octubre de 2026)

- [Atomic14 — Guition JC4827W543](https://www.atomic14.com/esp32/boards/guition-jc4827w543/) · [Sunton 8048S070](https://www.atomic14.com/esp32/boards/sunton-esp32-8048s070/) · [comparativa 7"](https://www.atomic14.com/esp32/boards/7-inch-displays/)
- [Tinytronics](https://www.tinytronics.nl/nl/development-boards/microcontroller-boards/met-wi-fi/jingcai-esp32-8048s070c-7-inch-tft-lcd-display-800*480-pixels-met-touchscreen-esp32-s3) · [Hobbyelectronica](https://www.hobbyelectronica.nl/en/product-categorie/platformen/esp32-display/)
- [Espressif GT911](https://components.espressif.com/components/espressif/esp_lcd_touch_gt911/versions/1.2.1/readme)
- [Hetzner CAX11](https://www.vpsbenchmarks.com/hosters/hetzner/plans/cax11) · [subida jun-2026](https://wz-it.com/en/blog/hetzner-price-increase-june-2026-cpx-ccx-alternatives/)
- [Oracle Always Free recortado](https://linuxiac.com/oracle-quietly-cuts-free-tier-ampere-a1-resources-in-half/) · [Cloudflare Durable Objects](https://developers.cloudflare.com/durable-objects/platform/pricing)
