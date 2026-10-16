# Documentación técnica — Pizarra sincronizada

Táctica: **cómo** se materializan las decisiones de [decisiones-de-diseno.md](decisiones-de-diseno.md) (se citan como D1…, A1…). Aquí no se discute el porqué.

Fecha: 2026-10-16 · Fase: recopilar alcance y costes antes de comprar o construir (D16)

> Precios: capturas de octubre de 2026. Cambian a menudo; confirma antes de comprar. *(estimación)* = cifra mía, sin fuente.

---

## 1. Visión general

```
  Casa A                                      Casa B
 ┌────────────────────┐   wss (TLS)   ┌────────────────────┐
 │ ESP32 ── pantalla  │◄─────────────►│ ESP32 ── pantalla  │
 │  (carcasa 3D, USB) │       ▲       │  (carcasa 3D, USB) │
 └────────────────────┘       │       └────────────────────┘
                       ┌──────┴──────┐
                       │  SERVIDOR   │  en casa del usuario (D14)
                       │ intermedio  │
                       └─────────────┘
```

Piezas: **aparato** (ESP32 + pantalla + carcasa, ×2), **servidor intermedio** y, aparte, el **lápiz** (D11).

---

## 2. Rutas de hardware

Tres rutas posibles. **D6 manda el montaje propio (A1/A2); la ruta B queda como alternativa visible.** Ninguna se empieza aún (D16).

| Ruta | Qué es | Dificultad | Coste por aparato |
|---|---|---|---|
| **A1** | Devkit ESP32-S3 + módulo de pantalla SPI con pines + cables | Baja | ~26–40 € *(sumando abajo)* |
| **A2** | Devkit ESP32-S3 + panel RGB suelto de 4,3" o 7" + placa adaptadora FPC diseñada por nosotros | Alta | Sin precio verificado del panel suelto |
| **B** (alternativa) | Placa única con ESP32 + pantalla ya montados | Muy baja | ~10–14 € (4,3") · ~21–28 € (7") |

### Qué es cada ruta

**A1 — SPI.** El ESP32 y la pantalla se conectan con unos 12 hilos: ~8 de pantalla (alimentación, CS, RESET, DC, MOSI, SCLK, luz de fondo) y 4 del táctil capacitivo (SDA, SCL, INT, RST), a 3,3 V. Sin PCB. Espressif recomienda SPI para 480×320 en el ESP32-S3 ([FAQ de Espressif](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html)).

**A2 — RGB.** Los paneles de 4,3" y 7" usan un cable plano FPC de 40 o 50 pines con unas 16 líneas de datos más sincronismos. No se puede cablear a mano: hace falta una **placa adaptadora propia** (KiCad, pedida a un fabricante de PCB) y sacar el pinout del datasheet del panel concreto. No he encontrado ninguna guía publicada para estos paneles sueltos. Avisos:
- En las placas integradas conocidas, el pin de interrupción del táctil GT911 viene sin conectar; sin él el procesador pasa del 80 % de uso ([ESP3D](https://esp3d.io/esp3d-tft/version_1x/hardware/esp32-s3/sunton-70-8048/)). En nuestro adaptador hay que cablearlo.
- A 800×480, Espressif da ~23 fps medios de LVGL en RGB; hacen falta framebuffers completos en PSRAM.

**B — Integrada.** Se compra ya montada. No hay que cablear ni diseñar PCB. Es la referencia de coste para decidir si merece la pena el montaje propio.

### Lista de compra: ruta A1 (por aparato)

| Pieza | Precio | Fuente |
|---|---|---|
| Devkit ESP32-S3 N16R8 (16 MB flash, 8 MB PSRAM) | ~6–10 € | [Bits & Parts](https://www.bitsandparts.nl/en/esp32-s3-devkitc-n16r8-wroom-uno-compatible-development-board-wifi-bluetoot-p1929708) (9,95 €, agotado al consultar) · eBay ~6–7 € de vendedores chinos |
| Módulo SPI 4,0" ST7796, 480×320, capacitivo | ~US$17,90 | [Elecrow DLS31040B](https://www.elecrow.com/4-spi-lcd-capacitive-touch-module-st7796-driver-320-480-resolution.html) (tiene conversor 3,3/5 V y cable FPC de 30 cm); [eBay](https://www.ebay.de/itm/203784153985) ~US$17,64 + 5,89 de envío |
| Cables dupont hembra-hembra | ~1–2 € *(estimación)* | cualquier tienda |
| Filamento de la carcasa | ~2–3 € *(estimación)* | tu impresora |
| **Total** | **~26–40 €** | |

⚠ No he podido confirmar qué controlador táctil lleva el módulo de Elecrow (FT6336, GT911…); confírmalo con ellos antes de pedir. Ojo con las versiones **resistivas** (XPT2046, p. ej. el [LCD Wiki MSP4021](https://lcdwiki.com/4.0inch_SPI_Module_ST7796)), que no valen.

### Alternativa integrada (ruta B), visible

| | 4,3" Guition JC4827W543C | 7" Sunton ESP32-8048S070C |
|---|---|---|
| Pantalla | 480×272 IPS, GT911 capacitivo | 800×480 IPS RGB, GT911 capacitivo |
| SoC | ESP32-S3: 4 MB flash, 8 MB PSRAM | ESP32-S3: 16 MB flash, 8 MB PSRAM |
| Precio calle (AliExpress/eBay) | ~US$10–14 | ~US$21–28 |
| Precio tienda UE | ~21 € (anuncio genérico sin confirmar) | 37,25 € (Tinytronics, agotado) · 52,95 € (Hobbyelectronica) |
| Pegas | 4 MB de flash; solo rotaciones 0°/180°; quirks de NV3041A | Revisiones v1.1/v1.3 con init distinto; parpadeo con wifi |
| Referencia | [Atomic14](https://www.atomic14.com/esp32/boards/guition-jc4827w543/) | [Atomic14](https://www.atomic14.com/esp32/boards/sunton-esp32-8048s070/) |

Tiendas UE de la 7": [Tinytronics](https://www.tinytronics.nl/nl/development-boards/microcontroller-boards/met-wi-fi/jingcai-esp32-8048s070c-7-inch-tft-lcd-display-800*480-pixels-met-touchscreen-esp32-s3), [Hobbyelectronica](https://www.hobbyelectronica.nl/en/product-categorie/platformen/esp32-display/).

### Comparativa de costes (dos aparatos)

| Ruta | Total estimado |
|---|---|
| A1 SPI 4,0" | ~52–80 € |
| A2 RGB 4,3"/7" con adaptador | sin precio verificado |
| B 4,3" integrada | ~20–28 € |
| B 7" integrada | ~42–56 € |

La diferencia entre A1 y B es el precio de elegir D6 (montaje propio sobre presupuesto).

### Compras comunes (una vez)

Cables USB-C (datos), cargadores/powerbank ≥2 A y, posiblemente, soldador si los pines del devkit vienen sin soldar. Lápiz (D11): aparte, ver §9.

### Compra (D13)

- **AliExpress/eBay:** buscar el código exacto; 2–4 semanas *(estimación)*; el precio no incluye envío ni IVA.
- **Tienda UE:** más rápida y con garantía, ~2×.

---

## 3. Alimentación (D5, D3)

USB-C a 5 V, cargador de ≥2 A; fuera de casa, powerbank. Sin batería: no hay cargador LiPo ni polaridades que cuidar. El aparato no lleva 4G: necesita una red wifi.

---

## 4. Conexión wifi (D2, D4, A1)

El aparato no lleva wifi precargado. Dos opciones, aún abiertas:

**(a) Bluetooth desde el móvil.** Espressif ofrece *Unified Provisioning* ([documentación](https://docs.espressif.com/projects/esp-idf/en/v5.2.1/esp32s3/api-reference/provisioning/provisioning.html)): el móvil envía la red y la contraseña por BLE, cifradas.
- *Security 1:* intercambio de claves Curve25519 y cifrado AES-256-CTR, con una clave de autorización opcional (PoP).
- *Security 2:* SRP6a y AES-256-GCM; las credenciales se generan antes y se pueden dar con una pegatina QR.
- Hace falta una app en el móvil. Existe una de Espressif, pero no he verificado su disponibilidad actual ni la licencia.

**(b) Portal cautivo.** El aparato crea su **propia red wifi** (no es la de tu router). El usuario se conecta desde el móvil y se abre el menú para elegir su wifi; si no salta solo, se abre una dirección en el navegador (por defecto 192.168.4.1 *(verificar)*). La contraseña viaja cifrada si la red del aparato es WPA2.

Ventaja común: como el aparato tiene pantalla, puede mostrar el nombre de red y un código (incluso un QR para conectarse) y demostrar presencia física.

Se guardan varias redes. Los portales con login de hotel no se soportan.

---

## 5. Servidor intermedio (D14, A4)

Programa en Node.js con [`ws`](https://github.com/websockets/ws) (MIT), un solo archivo, que se puede mover entre alojamientos.

| Alojamiento | Coste | Nota |
|---|---|---|
| **Servidor del usuario en casa** (elegido) | 0 € | Necesita llegar desde fuera |
| VPS pequeño | [Hetzner CAX11](https://www.vpsbenchmarks.com/hosters/hetzner/plans/cax11) ≈ 5,99 €/mes más IPv4 *(subió en 2026)* | Alternativa estable |
| Cloudflare Workers + Durable Objects | 0 € hasta los [límites](https://developers.cloudflare.com/durable-objects/platform/pricing) | Te ata a su API |
| Oracle "Always Free" | 0 € | No usar: [recortó el tier en 2026](https://linuxiac.com/oracle-quietly-cuts-free-tier-ampere-a1-resources-in-half/) |

**Cómo llega internet al servidor de casa (A4), pendiente de verificar:**
- Redirección de puertos en el router + DNS dinámico + proxy con TLS (p. ej. Caddy). Falla si el proveedor usa CGNAT.
- Túnel (p. ej. Cloudflare Tunnel): evita abrir puertos; condiciones del plan gratuito sin verificar.

El ESP32 necesita un certificado válido en el servidor para validar TLS (Let's Encrypt o similar).

---

## 6. Protocolo (borrador)

JSON por WebSocket (wss). Coordenadas en un lienzo lógico fijo; el aparato escala a su pantalla.

```
Cliente → servidor
  {"t":"join","room":"ABC123","key":"<secreto>"}
  {"t":"b","id":17,"c":"#e53935","w":6}        // empieza trazo
  {"t":"p","id":17,"pts":[[x,y],[x,y],...]}   // puntos del trazo
  {"t":"e","id":17}                             // fin de trazo
  {"t":"clear"}                                 // borrar todo
Servidor → cliente
  {"t":"snap","log":[...]}                      // al entrar: registro desde el último clear
  (reenvía b/p/e/clear del otro cliente en orden)
```

Sala = código de 6 caracteres + clave secreta, sin cuentas. El servidor numera los eventos y guarda el registro, así que quien se reconecta recibe `snap`. Cada aparato pinta en local al instante y luego envía, con puntos agrupados a ~30 Hz. La goma, el deshacer y los colores dependen de A5.

---

## 7. Calidad táctil (D15, A3)

Lo que determina que dibujar se sienta bien:

| Factor | Qué se hace |
|---|---|
| Lectura del táctil | Por **interrupción** (pin INT conectado), I2C a 400 kHz, en una tarea propia |
| Camino al píxel | El trazo se pinta en local sobre el lienzo sin pasar por la red |
| Envío a pantalla | Solo el rectángulo modificado, por SPI con DMA |
| Forma del trazo | Interpolar entre puntos (curva suave); evitar polígonos |
| Temblor de pulso | Filtro de suavizado que añada poca latencia (p. ej. [filtro 1€](https://gery.casiez.net/1euro/)) |
| Lienzo persistente | En PSRAM: 480×320×2 B ≈ 300 KB |

**Librerías gráficas (A3).** Explicación de lo que hacen:
- **LovyanGFX:** dibuja directo sobre un lienzo y trae soporte de táctil y DMA. Menos capas entre el dedo y el píxel; la interfaz (botones, menús) hay que escribirla a mano.
- **LVGL:** trae botones, menús y animaciones listos, pero actualiza la pantalla en ciclos (por defecto ~30 Hz) y añade capas.

Mi hipótesis, sin medir: LovyanGFX da menos latencia para el lienzo de dibujo. **No se decide hasta medir** la latencia del dedo al píxel con ambas en un prototipo. Licencias de cada una pendientes de verificar.

Lo que se medirá: latencia local (dedo→píxel), latencia entre aparatos, suavidad de línea y pérdida de puntos en trazos rápidos. Los valores objetivo quedan por fijar.

---

## 8. Caja (D12, D3)

Soporte inclinado con hueco para el USB-C y tornetas para sujetar pantalla y ESP32. Diseño paramétrico (OpenSCAD o CadQuery) en el repo, con los STL publicados. Para una pantalla suelta, el marco se mide sobre el módulo real; el datasheet no basta. Pendiente: medidas del módulo comprado.

---

## 9. Lápiz (D11)

Módulo aparte, ignorable en el desarrollo. Los táctiles capacitivos aceptan cualquier **lápiz capacitivo pasivo de punta fina** (disco o malla, ~1,5–2 mm); los activos (Apple Pencil, Wacom/AES, S-Pen) no funcionan. Precio ~1–5 € *(estimación)*; sin modelo verificado. Probar uno antes de comprar varios.

---

## 10. Qué comprar antes de decidir

Para saber si merece la pena (D16), el mínimo que da respuestas reales es **un aparato de la ruta A1** (~26–40 €) y, si se quiere comparar, **una placa integrada B de 4,3"** (~10–14 €). Con eso se mide la calidad táctil real y se comparan costes y esfuerzo. Todo lo demás (servidor, caja, A2) se puede escribir y diseñar sin gasto.

Dos pasos sin coste que se pueden hacer ya: cliente web de prueba con servidor intermedio (dos pestañas dibujando) y diseño de la carcasa en CAD.

---

## 11. Riesgos

| Riesgo | Mitigación |
|---|---|
| Latencia táctil visible | Medir antes de elegir librería; interrupción, DMA, interpolación |
| Controlador táctil del módulo distinto al esperado | Confirmar con el vendedor antes de comprar |
| A2 sin guías públicas | Empezar por A1; A2 solo con datasheet del panel concreto |
| Servidor de casa inaccesible (CGNAT) | Túnel o VPS pequeño |
| Cambian precios o stock | Dos fuentes de compra; confirmar antes de pedir |
| Portales con login (hoteles) | No soportados |
| El montaje propio no baja de ~26 € | Está asumido en D6; la ruta B queda como referencia |

---

## Fuentes (octubre de 2026)

- [Atomic14 — Guition JC4827W543](https://www.atomic14.com/esp32/boards/guition-jc4827w543/) · [Sunton 8048S070](https://www.atomic14.com/esp32/boards/sunton-esp32-8048s070/)
- [Elecrow — módulo SPI 4,0" capacitivo](https://www.elecrow.com/4-spi-lcd-capacitive-touch-module-st7796-driver-320-480-resolution.html) · [LCD Wiki ST7796](https://lcdwiki.com/4.0inch_SPI_Module_ST7796)
- [Bits & Parts — ESP32-S3 DevKitC N16R8](https://www.bitsandparts.nl/en/esp32-s3-devkitc-n16r8-wroom-uno-compatible-development-board-wifi-bluetoot-p1929708)
- [Espressif — FAQ de interfaces LCD](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html) · [Unified Provisioning](https://docs.espressif.com/projects/esp-idf/en/v5.2.1/esp32s3/api-reference/provisioning/provisioning.html)
- [ESP3D — nota sobre el INT del GT911](https://esp3d.io/esp3d-tft/version_1x/hardware/esp32-s3/sunton-70-8048/)
- [Tinytronics](https://www.tinytronics.nl/nl/development-boards/microcontroller-boards/met-wi-fi/jingcai-esp32-8048s070c-7-inch-tft-lcd-display-800*480-pixels-met-touchscreen-esp32-s3) · [Hobbyelectronica](https://www.hobbyelectronica.nl/en/product-categorie/platformen/esp32-display/)
- [Hetzner CAX11](https://www.vpsbenchmarks.com/hosters/hetzner/plans/cax11) · [Oracle Always Free recortado](https://linuxiac.com/oracle-quietly-cuts-free-tier-ampere-a1-resources-in-half/) · [Cloudflare Durable Objects](https://developers.cloudflare.com/durable-objects/platform/pricing)
