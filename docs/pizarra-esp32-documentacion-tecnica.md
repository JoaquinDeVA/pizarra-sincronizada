# Pizarra táctil sincronizada con ESP32 — Documentación técnica

Fecha: 2026-10-16 · Estado: borrador v2 (objetivo ~20 € por aparato) · No se ha construido nada

> Los precios son capturas de octubre de 2026 de buscadores y tiendas. **Cambian a menudo**; confirma en la tienda antes de comprar. Lo marcado como *(mi decisión)* o *(estimación)* es mío, no tuyo ni de una fuente.

---

## 1. Qué se quiere construir

Dos aparatos independientes, uno en cada casa, con pantalla táctil entre móvil y iPad. Cada persona dibuja con colores y borra, y la otra lo ve casi al instante. Nada más.

| Requisito | Consecuencia técnica |
|---|---|
| Cada uno en su casa | Sincronización por internet, no por radio local |
| Persona poco técnica | Se enciende y funciona; solo hay que elegir el wifi la primera vez |
| Portátil, también en coche | Alimentación por USB/powerbank; wifi cambiante (casa, oficina, hotspot del móvil) |
| ~20 € por aparato, "un poco más" | Placa de pantalla integrada de ~10–28 €; sin batería propia; caja impresa en 3D |
| Todo integrado en uno | Placa ESP32 con pantalla, táctil y wifi en un solo módulo |
| Sin software que pueda empezar a cobrar | Todo MIT/Apache/BSD o código propio; servidor intercambiable |
| Lápiz para dibujar | Módulo independiente, comprable por separado (ver 5) |

**Fuera de alcance:** vídeo/voz, cuentas de usuario, historial, más de 2 pantallas, imágenes importadas, resistencia al agua, batería integrada.
**Dentro de alcance:** diseño de la caja/soporte para impresora 3D (tienes impresora).

---

## 2. Decisiones de arquitectura

| Decisión | Elección | Por qué |
|---|---|---|
| Hardware | Placa ESP32-S3 con pantalla táctil capacitiva integrada | Decidido por ti. Las dos opciones elegidas incluyen todo |
| Sincronización | Un **relay** pequeño por WebSocket seguro (wss) en internet *(mi decisión)* | P2P directo falla tras NAT/CGNAT de datos móviles y necesitaría un servidor TURN. El relay son ~150 líneas y se puede mover de sitio |
| Orden de eventos | El relay numera los eventos; sin CRDT *(mi decisión)* | Con 2 personas y un servidor central basta el orden total |
| Borrar | Goma = trazo con el color del fondo; "borrar todo" vacía el registro *(mi decisión)* | Sin recalcular el lienzo; resultado idéntico en ambos lados |
| Lienzo | Lógico fijo, con bandas negras si las proporciones difieren *(mi decisión)* | Lo ideal es que ambos aparatos sean del mismo modelo |
| Wifi | Portal cautivo la primera vez, varias redes guardadas *(mi decisión)* | Persona no técnica; casa/oficina/coche |
| Licencias | Evitar tldraw | Su SDK exige licencia comercial de pago en producción: [licencia tldraw](https://tldraw.dev/sdk-features/license-key) |

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

---

## 3. Hardware: dos candidatos

Ambos son placas "todo en uno" de la familia de módulos chinos tipo Sunton/Guition. Se venden ya montadas; no hace falta diseñar PCB.

### Candidato A — Guition JC4827W543C, 4,3" (el más barato)

| Dato | Valor |
|---|---|
| Pantalla | 4,3" IPS, 480×272, controlador NV3041A por QSPI |
| Táctil | Capacitivo GT911 (variante **C**; existen R = resistiva y N = sin táctil) |
| SoC | ESP32-S3 (N4R8): 4 MB flash, 8 MB PSRAM |
| Conectividad | Wifi, BLE 5, USB-C, ranura SD |
| Batería | **No tiene conector** |
| Precio | ~US$10–14 de calle (Atomic14); un anuncio genérico similar en eBay.de a 20,99 € sin confirmar modelo |

Pegas confirmadas en [Atomic14](https://www.atomic14.com/esp32/boards/guition-jc4827w543/): solo 4 MB de flash (firmware ajustado), solo rotaciones 0° y 180°, hace falta invertir colores y byte-swap, y en Arduino_GFX el táctil y la SD pueden fallar justo tras refrescar si no se cuida el orden. Resolución gruesa, pero válida para dibujar.

### Candidato B — Sunton ESP32-8048S070C, 7"

| Dato | Valor |
|---|---|
| Pantalla | 7" IPS RGB, 800×480 |
| Táctil | Capacitivo GT911 |
| SoC | ESP32-S3: 16 MB flash, 8 MB PSRAM |
| Conectividad | Wifi, BLE 5, USB-C, ranura SD, altavoz |
| Batería | **No tiene** |
| Precio | ~US$21–28 de calle (Atomic14). En tiendas UE: 37,25 € en Tinytronics (ya no disponible) y 52,95 € en Hobbyelectronica |

Pegas en [Atomic14](https://www.atomic14.com/esp32/boards/sunton-esp32-8048s070/): las revisiones v1.1 y v1.3 necesitan código de inicio distinto, el framebuffer vive en PSRAM y con wifi activo puede aparecer parpadeo si no se ajusta LVGL, y quedan unos 2 GPIO libres. No me consta el consumo ni la alimentación exacta de esta placa; asume 5 V / 2 A por USB y compruébalo.

### Comparativa real de precios

| | A: 4,3" Guition | B: 7" Sunton |
|---|---|---|
| Precio calle (AliExpress/eBay) | ~10–14 € | ~21–28 € |
| Precio tiendas UE | ~21 € (anuncio genérico, sin confirmar) | ~37–53 € |
| Pantalla | 480×272 | 800×480 |
| Flash / PSRAM | 4 MB / 8 MB | 16 MB / 8 MB |
| Dentro de ~20 € | Sí, con margen | Roza o supera, según envío/IVA |

El precio de AliExpress no incluye envío ni IVA/aduanas, y desde esas tiendas tarda 2–4 semanas *(estimación)*.

### Dónde comprar (ambas fuentes)

- **AliExpress / eBay:** busca el código exacto, `JC4827W543C` o `ESP32-8048S070C`. Comprueba el sufijo **C** (capacitivo) y la revisión de la placa, y elige vendedor con buena reputación.
- **Tiendas UE:** más rápido y con garantía, pero ~2× el precio. Ejemplos con la placa de 7": [Tinytronics](https://www.tinytronics.nl/nl/development-boards/microcontroller-boards/met-wi-fi/jingcai-esp32-8048s070c-7-inch-tft-lcd-display-800*480-pixels-met-touchscreen-esp32-s3) y [Hobbyelectronica](https://www.hobbyelectronica.nl/en/product-categorie/platformen/esp32-display/). Para la de 4,3" no he localizado una tienda UE fiable.
- Comparativa de 7" de varios fabricantes: [Atomic14](https://www.atomic14.com/esp32/boards/7-inch-displays/).

### Presupuesto por aparato

| Pieza | A: 4,3" | B: 7" |
|---|---|---|
| Placa | ~10–14 € | ~21–28 € |
| Caja/soporte impreso en 3D | ~1–2 € de filamento *(estimación)* | ~2–3 € *(estimación)* |
| Cable USB-C + cargador | ya lo tienes | igual |
| Lápiz capacitivo (módulo aparte) | ~1–5 € *(estimación)* | igual |
| **Total** | **~12–21 €** | **~24–36 €** |

Sin batería propia, en el coche va con adaptador 12 V→USB o powerbank.

---

## 4. Servidor relay

Programa propio en Node.js con la librería [`ws`](https://github.com/websockets/ws) (MIT). Un solo archivo, portable a cualquier sitio.

| Opción | Coste | Comentario |
|---|---|---|
| **PC/Raspberry en casa + túnel** (p. ej. Cloudflare Tunnel) | ~0 € | Condiciones del túnel gratuito sin verificar aquí. Depende de que ese equipo esté encendido |
| **VPS pequeño** | [Hetzner CAX11](https://www.vpsbenchmarks.com/hosters/hetzner/plans/cax11) ≈ 5,99 €/mes más IPv4 *(subió en 2026)* | Lo más estable. Cabe en cualquier VPS de 3–6 €/mes |
| Cloudflare Workers + Durable Objects (gratis) | 0 € hasta los límites | [Límites](https://developers.cloudflare.com/durable-objects/platform/pricing): 100.000 peticiones/día; te ata a su API |
| Oracle Cloud "Always Free" | 0 € | **No recomendado**: en junio de 2026 [recortó el tier gratuito a la mitad](https://linuxiac.com/oracle-quietly-cuts-free-tier-ampere-a1-resources-in-half/) sin avisar |

**Recomendación *(mi decisión)*:** empezar en tu PC o Raspberry con túnel, y mover el mismo archivo a un VPS si quieres disponibilidad continua.

---

## 5. Lápiz (módulo independiente)

No forma parte del desarrollo: se compra aparte y se puede ignorar.

- Los dos candidatos usan el controlador GT911, **capacitivo**. Sirve cualquier **lápiz capacitivo pasivo de punta fina** (punta de disco o malla, ~1,5–2 mm). Los lápices activos (Apple Pencil, Wacom/AES, S-Pen) **no** funcionan.
- Precio *(estimación)*: ~1–5 € por lápiz. No he podido verificar un modelo concreto; busca "capacitive stylus fine tip disc" y compra uno barato para probar.
- Cuidado: las puntas muy finas pueden registrarse débil o errática en un táctil afinado para dedos. **Prueba un lápiz antes de comprar varios**, y suaviza las coordenadas en el firmware (media móvil simple).
- Quedan fuera: lápiz con presión, rechazo de palma y botones del lápiz.

---

## 6. Protocolo (borrador)

JSON por WebSocket para empezar; se puede pasar a binario si hace falta.

```
Cliente → relay
  {"t":"join","room":"ABC123","key":"<secreto>"}
  {"t":"b","id":17,"c":"#e53935","w":6}        // empieza trazo (color, grosor; goma = color del fondo)
  {"t":"p","id":17,"pts":[[x,y],[x,y],...]}   // puntos en coordenadas lógicas
  {"t":"e","id":17}                             // fin de trazo
  {"t":"clear"}                                 // borrar todo
Relay → cliente
  {"t":"snap","log":[...]}                      // al entrar: registro desde el último clear
  (reenvía b/p/e/clear del otro cliente en orden)
```

Sala = código de 6 caracteres + clave secreta. Sin cuentas. El relay guarda el registro y lo reenvía a quien se reconecta. Límite de registro (p. ej. 50.000 puntos) *(mi decisión)*. Cada aparato dibuja en local al instante.

---

## 7. Firmware

| Pieza | Elección | Licencia |
|---|---|---|
| Entorno | PlatformIO + Arduino-ESP32 *(mi decisión)* | Apache 2.0 / LGPL 2.1 |
| Gráficos y táctil | [LVGL 9](https://lvgl.io) (canvas) | MIT |
| WebSocket cliente | `esp_websocket_client` o [arduinoWebSockets](https://github.com/Links2004/arduinoWebSockets) | Apache 2.0 / LGPL 2.1 |
| Provisión wifi | Portal cautivo propio o [WiFiManager](https://github.com/tzapu/WiFiManager) | MIT |
| TLS | mbedTLS incluido | Apache 2.0 |

- **Memoria A:** framebuffer 480×272×2 B ≈ 255 KB en PSRAM; el firmware debe caber en 4 MB de flash.
- **Memoria B:** 800×480×2 B ≈ 750 KB en PSRAM; ajustar bounce buffers de LVGL contra el parpadeo con wifi.
- Se pinta solo el segmento nuevo, no todo el lienzo.
- UI mínima: ~8 colores, 3 grosores, goma, "borrar todo" con confirmación, indicador de conexión.
- Reconexión automática y varias redes wifi.
- Verifica la licencia de cualquier driver o ejemplo del fabricante antes de reutilizar su código.

---

## 8. Caja y soporte (impresión 3D, dentro de alcance)

- Un soporte inclinado con hueco para el USB-C y marco que sujete la placa; sin batería, así que es más simple.
- Diseño paramétrico (p. ej. OpenSCAD o CadQuery) en el repo, para ajustar al modelo A o B y publicar los STL. *(mi decisión)*
- Pendiente: medidas exactas de placa y posición de tornillos/conectores del lote que compres, porque varían por revisión.

---

## 9. Plan por fases

1. **Relay + cliente web de prueba** (coste 0): dos pestañas dibujando. → valida protocolo, reconexión y `clear`.
2. **Una sola placa barata (A)**, ~10–14 €, para probar táctil, lápiz y wifi. → decide si 4,3" basta o hace falta 7".
3. **Firmware v1:** dibujar y borrar en local → sincronizar → portal wifi.
4. **Caja impresa** y prueba con lápiz.
5. **Segunda unidad** y prueba real en dos casas y con hotspot.

---

## 10. Riesgos

| Riesgo | Mitigación |
|---|---|
| 4,3" demasiado pequeña o gruesa | Prototipar primero con una sola placa; plan B = placa de 7" |
| Placa 7" ajusta mal el precio tras envío/IVA | Calcular total con envío antes de pedir |
| Revisiones de placa con init distinto | Anotar revisión al comprar; probar con una unidad |
| 4 MB de flash en la de 4,3" | Firmware ligero, sin ESPHome ni librerías pesadas |
| Parpadeo de la 7" con wifi | Ajustes de LVGL (bounce buffers) |
| Lápiz poco fiable | Probar uno antes; suavizado de coordenadas |
| Portales de login de hoteles | No soportados; usar hotspot del móvil |
| Cambian precios o stock | Dos fuentes de compra; confirmar antes de pedir |

---

## 11. Pendiente de decidir

- ¿Empezamos con una sola placa A de ~12 € como prototipo?
- ¿Relay en casa (túnel gratuito) o servidor de pago?
- ¿Mismo modelo en ambas casas o pueden ser distintos?
- ¿8 colores, 3 grosores y sin deshacer en v1?

---

## Fuentes (octubre de 2026)

- [Atomic14 — Guition JC4827W543](https://www.atomic14.com/esp32/boards/guition-jc4827w543/)
- [Atomic14 — Sunton ESP32-8048S070](https://www.atomic14.com/esp32/boards/sunton-esp32-8048s070/)
- [Atomic14 — comparativa de pantallas 7"](https://www.atomic14.com/esp32/boards/7-inch-displays/)
- [Tinytronics — 8048S070C](https://www.tinytronics.nl/nl/development-boards/microcontroller-boards/met-wi-fi/jingcai-esp32-8048s070c-7-inch-tft-lcd-display-800*480-pixels-met-touchscreen-esp32-s3) · [Hobbyelectronica](https://www.hobbyelectronica.nl/en/product-categorie/platformen/esp32-display/)
- [Espressif — driver esp_lcd_touch_gt911](https://components.espressif.com/components/espressif/esp_lcd_touch_gt911/versions/1.2.1/readme)
- [Hetzner CAX11](https://www.vpsbenchmarks.com/hosters/hetzner/plans/cax11) · [subida de precios jun-2026](https://wz-it.com/en/blog/hetzner-price-increase-june-2026-cpx-ccx-alternatives/)
- [Oracle Always Free recortado](https://linuxiac.com/oracle-quietly-cuts-free-tier-ampere-a1-resources-in-half/)
- [Cloudflare Durable Objects: precios](https://developers.cloudflare.com/durable-objects/platform/pricing)
- [tldraw: clave de licencia](https://tldraw.dev/sdk-features/license-key)
