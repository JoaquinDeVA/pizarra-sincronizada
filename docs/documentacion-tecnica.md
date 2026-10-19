# Documentación técnica — Pizarra sincronizada

Táctica: **cómo** se materializan las decisiones de [decisiones-de-diseno.md](decisiones-de-diseno.md) (se citan como D1…, A1…). Aquí no se discute el porqué.

Fecha: 2026-10-22

> Precios: capturas de octubre de 2026. Cambian a menudo; confirma antes de comprar. *(estimación)* = cifra mía, sin fuente.

---

## 1. Visión general

```
  Casa A                                      Casa B
 ┌────────────────────┐   wss (TLS)   ┌────────────────────┐
 │ Placa ESP32 + LCD  │◄─────────────►│ Placa ESP32 + LCD  │
 │ (caja 3D, USB-C)   │       ▲       │ (caja 3D, USB-C)   │
 └────────────────────┘       │       └────────────────────┘
                       ┌──────┴──────┐
                       │  SERVIDOR   │  en casa del usuario (D14)
                       └─────────────┘
```

**Estructura del repo (D16):** dos directorios, `firmware/` (pizarras) y `server/`, cada uno con su `.env` fuera del repo. El lápiz (D11) y la carcasa (D12) son piezas aparte.

---

## 2. Hardware (D7, D10, D17)

Placa ya montada **Waveshare ESP32-S3-Touch-LCD-7**, versión *Touch*. La comparación con otras placas vive en [compra/](compra/README.md).

| Dato | Valor |
|---|---|
| Pantalla | 7" IPS RGB 800×480, controlador ST7262 |
| Táctil | GT911 de 5 puntos; INT en GPIO4, SDA GPIO8, SCL GPIO9, RST por el expansor CH422G (EXIO1) |
| SoC | ESP32-S3-WROOM-1 N16R8: 16 MB flash, 8 MB PSRAM, 512 KB SRAM |
| Conectividad | Wifi 2,4 GHz, BLE 5 |
| Alimentación | USB-C a 5 V, 450 mA según la wiki |
| Batería | Conector PH2.0 con cargador, sin usar (D5) |
| Tamaño | 192,96 × 110,76 mm con táctil |
| Documentación | [Wiki](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7) · [esquema](https://files.waveshare.com/wiki/ESP32-S3-Touch-LCD-7/ESP32-S3-Touch-LCD-7-Sch.pdf) · [GT911](https://files.waveshare.com/wiki/common/GT911_EN_Datasheet.pdf) · [ST7262](https://files.waveshare.com/wiki/common/ST7262.pdf) |

Notas para D15 (calidad táctil):
- Flash y PSRAM comparten SPI1: una escritura en flash puede detener el panel y causar jitter ([Atomic14](https://www.atomic14.com/esp32/boards/waveshare-esp32-s3-touch-lcd-7/)). Mitigación: PSRAM octal a 120 MHz con XIP-from-PSRAM, o ESP-IDF, y no escribir en flash mientras se dibuja.
- Quedan ~4 GPIO libres; el resto pasa por el CH422G.
- A 800×480, Espressif da ~23 fps medios de LVGL en RGB ([FAQ](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html)).
- La wiki da 16 MB de flash en un apartado y 8 MB en otro: confirmar en la placa recibida.

### Compra (D13)

- **AliExpress/eBay:** buscar el código exacto (`ESP32-8048S070C`). Comprobar el sufijo **C** (capacitivo) y la revisión. 2–4 semanas *(estimación)*; el precio no incluye envío ni IVA.
- **Tienda UE:** más rápida y con garantía, ~2×.
- La guía de compra con todas las opciones vive en `docs/compra/`.

### Coste (D6)

Dos aparatos: ~75–120 € entregados en España según la [guía de compra](compra/02-vendedores.md). Más ~1–3 € de filamento por caja *(estimación)*. Cable USB-C y cargador ≥2 A ya los tienes.

---

## 3. Alimentación (D5)

USB-C constante, cargador de ≥2 A. Sin batería: no hay cargador ni polaridades que cuidar.

---

## 4. Conexión wifi (D2, D4, D25)

El aparato no lleva wifi precargado. El menú de ajustes lista las redes visibles; el usuario elige una y escribe la contraseña con un teclado en la propia pantalla. Se guardan varias redes y el aparato intenta las conocidas al encender.

---

## 5. Servidor (D14, D20–D24)

Programa en Node.js con [`ws`](https://github.com/websockets/ws) (MIT), en el servidor de casa del usuario. Coste 0 €.

**Estructura:** un módulo escucha en un puerto de forma pasiva; cada conexión nueva se entrega a un módulo de sesión y el listener vuelve a escuchar (D21).

**Acceso (D22):**
- La pantalla envía el secreto como primer mensaje. El servidor compara su hash con el de su `.env` (comparación en tiempo constante). La pantalla lleva el mismo secreto en su `.env`.
- Conexión sin autenticar en pocos segundos: se cierra. Intentos fallidos limitados por IP.
- Ambos `.env` se quedan fuera del repo; el repo lleva un `.env.example` sin valores.

**Sala (D23, D24):** una sola sala. Dos pantallas autenticadas como máximo. El servidor guarda en memoria el registro de trazos y lo reenvía al reconectar. Si una pantalla vuelve mientras queda abierta una conexión antigua, la antigua se sustituye.

**Cómo llega internet al servidor de casa (A3), pendiente de verificar:**
- Redirección de puertos en el router + DNS dinámico + proxy con TLS (p. ej. Caddy). Falla si el proveedor usa CGNAT.
- Túnel (p. ej. Cloudflare Tunnel): evita abrir puertos; condiciones del plan gratuito sin verificar.

El ESP32 necesita un certificado válido en el servidor para validar TLS (Let's Encrypt o similar).

---

## 6. Protocolo (borrador)

JSON por WebSocket (wss). Coordenadas en un lienzo lógico fijo; el aparato escala a su pantalla.

```
Cliente → servidor
  {"t":"auth","secret":"<secreto del .env>"}   // primer mensaje obligatorio
  {"t":"b","id":17,"c":"#e53935","w":6}        // empieza trazo
  {"t":"p","id":17,"pts":[[x,y],[x,y],...]}   // puntos del trazo
  {"t":"e","id":17}                             // fin de trazo
  {"t":"clear"}                                 // borrar todo
Servidor → cliente
  {"t":"ok"} | {"t":"peer","on":true|false}    // autenticado / la otra pantalla entra o sale
  {"t":"snap","log":[...]}                      // al entrar: registro desde el último clear
  (reenvía b/p/e/clear del otro cliente en orden)
```

Una sola sala fija, sin cuentas. El servidor numera los eventos y guarda el registro, así que quien se reconecta recibe `snap`. Cada aparato pinta en local al instante y luego envía, con puntos agrupados a ~30 Hz. Colores, goma y deshacer dependen de A4.

---

## 7. Calidad táctil (D15, A2)

Lo que determina que dibujar se sienta bien:

| Factor | Qué se hace |
|---|---|
| Lectura del táctil | Por **interrupción** (pin INT conectado), I2C a 400 kHz, en una tarea propia |
| Camino al píxel | El trazo se pinta en local sobre el lienzo sin pasar por la red |
| Envío a pantalla | Solo el rectángulo modificado |
| Forma del trazo | Interpolar entre puntos (curva suave) |
| Temblor de pulso | Filtro de suavizado que añada poca latencia (p. ej. [filtro 1€](https://gery.casiez.net/1euro/)) |
| Lienzo persistente | En PSRAM (800×480×2 B ≈ 750 KB) |

**Librerías gráficas (A2).**
- **LovyanGFX:** dibuja directo sobre un lienzo y trae soporte de táctil y DMA. Menos capas entre el dedo y el píxel; la interfaz (botones, menús) hay que escribirla a mano.
- **LVGL:** trae botones, menús y animaciones listos, pero actualiza la pantalla en ciclos (por defecto ~30 Hz) y añade capas.

Mi hipótesis, sin medir: LovyanGFX da menos latencia en el lienzo. **No se decide hasta medir** la latencia del dedo al píxel con ambas en la placa real. No he verificado su soporte para estas dos placas concretas ni sus licencias.

Se medirá: latencia local (dedo→píxel), latencia entre aparatos, suavidad de línea y pérdida de puntos en trazos rápidos. Los valores objetivo quedan por fijar.

---

## 8. Caja (D12, D3)

Soporte inclinado con hueco para el USB-C. Diseño paramétrico (OpenSCAD o CadQuery) en el repo, con los STL publicados. Pendiente: medidas de la placa comprada, que varían por revisión.

---

## 9. Lápiz (D11)

Módulo aparte, ignorable en el desarrollo. Los táctiles capacitivos aceptan cualquier **lápiz capacitivo pasivo de punta fina** (disco o malla, ~1,5–2 mm); los activos (Apple Pencil, Wacom/AES, S-Pen) no funcionan. Precio ~1–5 € *(estimación)*; sin modelo verificado. Probar uno antes de comprar varios.

---

## 10. Riesgos

| Riesgo | Mitigación |
|---|---|
| Latencia táctil visible | Medir antes de elegir librería; interrupción, interpolación |
| Jitter por escrituras en flash | XIP-from-PSRAM; no escribir en flash mientras se dibuja |
| Revisión de placa distinta a la esperada | Anotar revisión al comprar; probar una unidad primero |
| Servidor de casa inaccesible desde fuera (CGNAT) | Túnel |
| Cambian precios o stock | Dos fuentes de compra; confirmar antes de pedir |

---

## Fuentes (octubre de 2026)

- [Atomic14 — Waveshare ESP32-S3-Touch-LCD-7](https://www.atomic14.com/esp32/boards/waveshare-esp32-s3-touch-lcd-7/) · [Waveshare wiki](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7)
- [Espressif — FAQ de interfaces LCD](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html) · [Unified Provisioning](https://docs.espressif.com/projects/esp-idf/en/v5.2.1/esp32s3/api-reference/provisioning/provisioning.html)
