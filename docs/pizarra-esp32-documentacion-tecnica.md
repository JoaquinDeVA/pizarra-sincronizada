# Pizarra táctil sincronizada con ESP32 — Documentación técnica

Fecha: 2026-10-16 · Estado: borrador para aprobación (no se ha construido nada)

> Los precios son capturas de octubre de 2026 de buscadores/tiendas. **Cambian a menudo**; confirma en la tienda antes de comprar. Lo marcado como *(mi decisión)* o *(estimación)* es mío, no tuyo ni de una fuente.

---

## 1. Qué se quiere construir

Dos aparatos independientes, uno en cada casa, con pantalla táctil entre móvil y iPad. Cada persona dibuja con colores y borra, y la otra lo ve casi al instante. Nada más.

**Requisitos (los tuyos):**

| Requisito | Consecuencia técnica |
|---|---|
| Cada uno en su casa | Sincronización por internet, no por radio local |
| Persona poco técnica | Se enciende y funciona; nada de configurar a mano más allá de elegir wifi la primera vez |
| Portátil, también en coche | Batería pequeña o USB; el wifi cambia (casa, oficina, hotspot del móvil) |
| Sin entornos agresivos | No hace falta carcasa IP; basta una caja normal |
| Barato y adaptado a lo pedido | ESP32 con pantalla integrada, sin Linux ni tablet |
| Sin software que pueda empezar a cobrar | Todo MIT/Apache/BSD o código propio; servidor intercambiable |

**Fuera de alcance (non-goals):** vídeo/voz, cuentas de usuario, historial de dibujos, más de 2 pantallas, imágenes importadas, resistencia al agua.

---

## 2. Decisiones de arquitectura

| Decisión | Elección | Por qué |
|---|---|---|
| Hardware | ESP32-S3 (o P4) con pantalla táctil RGB de 7" integrada | Decidido por ti. Placa única, wifi, conector de batería, unos 35–60 € |
| Sincronización | Un **relay** pequeño por WebSocket seguro (wss) en internet; los dos aparatos se conectan a él *(mi decisión)* | P2P directo falla tras NAT/CGNAT de datos móviles y necesitaría un servidor TURN (que suele costar). El relay es ~150 líneas y se puede mover de sitio |
| Orden de eventos | El relay numera los eventos; no hay CRDT *(mi decisión)* | Con 2 personas y un servidor central basta orden total. Mucho más simple que Yjs/Automerge |
| Borrar | Goma = trazo con el color del fondo; "borrar todo" vacía el registro *(mi decisión)* | Sin recalcular el lienzo ni detectar colisiones; el resultado es idéntico en ambos lados |
| Lienzo | Lógico fijo de 1600×1000 (16:10), coordenadas enviadas escaladas *(mi decisión)* | Así dos pantallas con resolución distinta ven lo mismo |
| Wifi | Portal cautivo la primera vez (el aparato crea su propia red, eliges tu wifi desde el móvil); guarda varias redes *(mi decisión)* | Persona no técnica; casa/oficina/coche |
| Licencias | Evitar tldraw | Su SDK exige licencia comercial de pago (sin marca de agua) en producción: [licencia tldraw](https://tldraw.dev/sdk-features/license-key). Justo el riesgo que quieres evitar |

### Esquema

```
  Casa A                                   Casa B
 ┌────────────┐      wss (TLS)       ┌────────────┐
 │ ESP32 7"   │◄────────────────────►│ ESP32 7"   │
 │ LVGL canvas│        ▲     ▲       │ LVGL canvas│
 └────────────┘        │     │       └────────────┘
                   ┌───┴─────┴───┐
                   │   RELAY     │  sala = código; guarda el registro
                   │ (Node, ~150 │  de trazos para quien se reconecta
                   │  líneas)    │
                   └─────────────┘
```

---

## 3. Hardware: dos modelos

Ambos son placas "todo en uno" (MCU + pantalla + táctil + wifi). No hace falta diseñar ni fabricar PCB: ya se venden.

### Modelo BARATO — Waveshare ESP32-S3-Touch-LCD-7

| Dato | Valor |
|---|---|
| SoC | ESP32-S3N16R8 (2 núcleos, 240 MHz), 16 MB flash, 8 MB PSRAM |
| Pantalla | 7", 800×480, 65K colores, táctil capacitivo GT911 de 5 puntos |
| Conectividad | Wifi 2,4 GHz, BLE 5 |
| Batería | Conector PH2.0 para LiPo 3,7 V de 1 celda, con cargador y LED de carga |
| USB | Type-C (más otro Type-C para UART) |
| Caja | No incluida |
| Precio | US$31,99–36,99 en tienda oficial; £35,60 en The Pi Hut; ~US$44 + ~US$12 envío en eBay |

Enlaces: [Waveshare (oficial)](https://www.waveshare.com/esp32-s3-touch-lcd-7.htm) · [The Pi Hut](https://thepihut.com/collections/waveshare/products/esp32-s3-development-board-with-7-capacitive-touch-display-800-x-480) · [wiki/documentación Waveshare](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7)

Al comprar, elige la variante **"with Touch"** (hay una sin táctil) y antena integrada.

### Modelo MODERADO — Waveshare ESP32-P4-WIFI6-Touch-LCD-7B

| Dato | Valor |
|---|---|
| SoC | ESP32-P4 (más potente) con wifi 6 por coprocesador |
| Pantalla | 7", 1024×600, táctil de 5 puntos |
| Precio | US$46,99–55,99 (Waveshare) |

Enlace: [catálogo ESP32-P4 de Waveshare](https://www.waveshare.com/product/esp32-related/boards-kits/esp32-p4.htm) · [cobertura de CNX Software](https://www.cnx-software.com/2026/01/17/tablet-like-esp32-p4-based-7-8-and-10-1-inch-hmi-displays-integrate-wi-fi-6-connectivity/)

⚠ Datos de esta placa salen de un solo resultado de búsqueda; verifica pantalla, batería y precio en la ficha antes de comprar. El P4 es más reciente: **peor documentado y con wifi vía coprocesador**, así que el firmware cuesta más.

### Alternativa intermedia — Elecrow CrowPanel Advance 7"

ESP32-S3, 16 MB flash, 8 MB PSRAM, 800×480 IPS, alimentación 5 V/2 A. Unos 50–53 € en [Openelab](https://openelab.io/a/s/products/elecrow-crowpanel-advance-7-hmi-esp32-s3-ai-touch-screen) y [Bits & Parts](https://www.bitsandparts.nl/en/esp32-s3-display-crowpanel-advance-7-inch-ips-touch-screen-elecrow-p1933868). Las fuentes se contradicen sobre la batería: confirmarlo antes. La versión [CrowPanel 7" con caja acrílica](https://uk.robotshop.com/products/elecrow-crowpanel-70-hmi-esp32-display-800x480-rgb-tft-lcd-touchscreen-w-case) ronda £44.

### Lista de compra por aparato (×2)

| Pieza | Barato | Moderado |
|---|---|---|
| Placa | ~35–45 € | ~50–65 € |
| LiPo 3,7 V PH2.0 (2000–3000 mAh) | ~6–10 € *(estimación)* | igual |
| Caja/soporte (impresa en 3D o pegatinas+marco) | 0–10 € *(estimación)* | igual |
| Cable USB-C + cargador | ya lo tienes | igual |
| **Total por aparato** | **~45–65 €** | **~65–85 €** |
| **Total dos aparatos** | **~90–130 €** | **~130–170 €** |

**Cuidado con la batería:** la polaridad de los conectores PH2.0 varía entre fabricantes; comprueba + / − con el esquema de la placa antes de enchufar (un invertido quema la placa).
Autonomía *(estimación, sin medir)*: pantalla de 7" encendida ≈ 1–1,5 W → 3000 mAh dan ~6–8 h. Se puede bajar con brillo y apagando la pantalla tras inactividad.

### Coche
Adaptador 12 V→USB-C y wifi del hotspot del móvil. No hay 4G en el aparato: **sin datos del móvil no hay sincronización**. Añadir módem 4G subiría coste y complejidad; no se contempla.

---

## 4. Servidor relay: coste y riesgo

Es lo único que corre "en internet". Programa propio en Node.js con la librería [`ws`](https://github.com/websockets/ws) (MIT). Un solo archivo, portable a cualquier sitio.

| Opción | Coste | Comentario |
|---|---|---|
| **Un PC/Raspberry en casa + túnel** (p. ej. Cloudflare Tunnel) | ~0 € (dominio opcional) | Sin verificar aquí condiciones actuales del túnel gratuito. Depende de que ese equipo esté encendido |
| **VPS pequeño** | [Hetzner CAX11](https://www.vpsbenchmarks.com/hosters/hetzner/plans/cax11) ≈ 5,99 €/mes más IPv4 *(subió dos veces en 2026)* | Lo más estable y sencillo. Cabe en cualquier VPS de 3–5 €/mes |
| Cloudflare Workers + Durable Objects (gratis) | 0 € hasta los límites | [Límites y precios](https://developers.cloudflare.com/durable-objects/platform/pricing): 100.000 peticiones/día, cada 20 mensajes WebSocket cuentan como 1. Con este uso casi seguro cabe, pero te ata a su API |
| Oracle Cloud "Always Free" | 0 € | **No recomendado**: en junio de 2026 [redujo el tier gratuito a la mitad](https://linuxiac.com/oracle-quietly-cuts-free-tier-ampere-a1-resources-in-half/) sin avisar. Ejemplo de lo que quieres evitar |
| Brokers MQTT públicos | 0 € | Sin autenticación ni garantías ni historial para reconexión. No recomendado |

**Recomendación *(mi decisión)*:** empezar en tu PC/Raspberry con túnel (coste 0) y, si quieres que funcione siempre, mover el mismo archivo a un VPS de ~4–6 €/mes. Ninguna opción te encierra porque el relay no usa nada propietario.

Coste de red: los puntos se agrupan a ~30 Hz y caben en pocos kB/s, de sobra para 4G.

---

## 5. Protocolo (borrador)

JSON por WebSocket para empezar (legible y depurable); se puede pasar a binario si hace falta.

```
Cliente → relay
  {"t":"join","room":"ABC123","key":"<secreto>"}
  {"t":"b","id":17,"c":"#e53935","w":6}        // empieza trazo (color, grosor; goma = color del fondo)
  {"t":"p","id":17,"pts":[[x,y],[x,y],...]}   // puntos del trazo, 0..1599 × 0..999
  {"t":"e","id":17}                             // fin de trazo
  {"t":"clear"}                                 // borrar todo
Relay → cliente
  {"t":"snap","log":[...]}                      // al entrar: registro completo desde el último clear
  (reenvía b/p/e/clear del otro cliente en orden)
```

- Sala = código de 6 caracteres + clave secreta; así nadie ajeno entra. Sin cuentas.
- El relay guarda el registro en memoria (y en disco, opcional). Si un aparato se desconecta y vuelve, recibe `snap` y queda igual.
- Límite del registro (p. ej. 50.000 puntos); al superarlo, aviso en pantalla o `clear` sugerido *(mi decisión)*.
- Cada aparato dibuja en local al instante y envía; el otro dibuja al recibir.

---

## 6. Firmware

| Pieza | Elección | Licencia |
|---|---|---|
| Entorno | Arduino-ESP32 *(mi decisión; ESP-IDF si hace falta rendimiento)* | LGPL 2.1 / Apache 2.0 |
| Gráficos y táctil | [LVGL 9](https://lvgl.io) (canvas) + driver de panel de Waveshare | MIT |
| WebSocket cliente | `esp_websocket_client` (ESP-IDF) o [arduinoWebSockets](https://github.com/Links2004/arduinoWebSockets) | Apache 2.0 / LGPL 2.1 |
| Provisión wifi | Portal cautivo propio o [WiFiManager](https://github.com/tzapu/WiFiManager) | MIT |
| TLS | mbedTLS incluido (wss) | Apache 2.0 |

Notas de diseño:
- Lienzo en PSRAM: 800×480×2 B ≈ 750 KB de los 8 MB. Sobra.
- La tinta se pinta por segmentos sobre el canvas al recibir cada punto; no se redibuja todo.
- UI mínima: paleta de ~8 colores, 2–3 grosores, goma, "borrar todo" con confirmación, indicador de conexión.
- Reconexión automática con espera creciente; guarda varias redes wifi.
- Apagado de pantalla tras inactividad para ahorrar batería.

Verifica licencias exactas de los drivers/ejemplos que descargues de Waveshare antes de reutilizar su código.

---

## 7. Plan por fases

1. **Relay + cliente web de prueba** (sin hardware, coste 0): dos pestañas dibujando. → verifica protocolo, reconexión y `clear`.
2. **Comprar 2× placa barata + 2 baterías.** → verifica que enciende, táctil y wifi.
3. **Firmware v1**: dibujar y borrar en local → luego sincronizar con el relay → luego portal wifi.
4. **Caja y batería** (impresión 3D o soporte comprado).
5. **Prueba real** en dos casas y con hotspot en el coche.
6. Si la resolución o la fluidez no basta, pasar al modelo moderado (el relay y el protocolo no cambian).

---

## 8. Riesgos

| Riesgo | Mitigación |
|---|---|
| Latencia visible con dos aparatos lejanos | Dibujo local inmediato; lotes de puntos a ~30 Hz; relay cercano |
| Fluidez del trazo en ESP32 | Pintar solo segmentos nuevos; PSRAM; si falla, modelo P4 |
| Wifi cambiante/portal cautivo de hoteles | Guardar varias redes; hotspot de móvil como plan B; **los portales de login de hotel no se soportan** |
| Placa o batería con polaridad distinta | Revisar esquema; comprar batería con el conector correcto |
| Precios e disponibilidad cambian | Dos fuentes por modelo; confirmar antes de pedir |
| El relay cae | Registro en disco; aparatos reintentan; relay fácil de reubicar |

---

## 9. Pendiente de decidir contigo

- ¿País y moneda de compra (envío y aduanas desde China/EE. UU. cambian el precio)?
- ¿Quieres seguir con el modelo barato para el prototipo o ir directo al moderado?
- ¿Quién hospedará el relay: equipo en casa o VPS de pago pequeño?
- ¿Con qué herramienta prefieres desarrollar el firmware (Arduino IDE, PlatformIO, ESP-IDF)?
- ¿Impresora 3D disponible para la caja?

---

## Fuentes consultadas (octubre de 2026)

- [Waveshare ESP32-S3-Touch-LCD-7](https://www.waveshare.com/esp32-s3-touch-lcd-7.htm)
- [The Pi Hut — ESP32-S3 7" Touch](https://thepihut.com/collections/waveshare/products/esp32-s3-development-board-with-7-capacitive-touch-display-800-x-480)
- [Waveshare ESP32-P4](https://www.waveshare.com/product/esp32-related/boards-kits/esp32-p4.htm) · [CNX Software](https://www.cnx-software.com/2026/01/17/tablet-like-esp32-p4-based-7-8-and-10-1-inch-hmi-displays-integrate-wi-fi-6-connectivity/)
- [Elecrow CrowPanel Advance 7" (Openelab)](https://openelab.io/a/s/products/elecrow-crowpanel-advance-7-hmi-esp32-s3-ai-touch-screen) · [Bits & Parts](https://www.bitsandparts.nl/en/esp32-s3-display-crowpanel-advance-7-inch-ips-touch-screen-elecrow-p1933868)
- [Hetzner CAX11 (vpsbenchmarks)](https://www.vpsbenchmarks.com/hosters/hetzner/plans/cax11) · [subida de precios jun-2026](https://wz-it.com/en/blog/hetzner-price-increase-june-2026-cpx-ccx-alternatives/)
- [Oracle Always Free recortado](https://linuxiac.com/oracle-quietly-cuts-free-tier-ampere-a1-resources-in-half/)
- [Cloudflare Durable Objects: precios](https://developers.cloudflare.com/durable-objects/platform/pricing)
- [tldraw: clave de licencia](https://tldraw.dev/sdk-features/license-key)
