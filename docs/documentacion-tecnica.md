# Documentación técnica — Cuadro e-ink

Táctica: **cómo** se materializan las decisiones de [decisiones-de-diseno.md](decisiones-de-diseno.md) (se citan como D1…, A1…). Aquí no se discute el porqué.

Fecha: 2026-10-27

> Precios y plazos de la [guía de compra](compra/README.md). *(estimación)* = cifra mía, sin fuente.

---

## 1. Visión general

```
 Fuentes de contenido            SERVIDOR (casa del usuario)          Cuadro e-ink
 ┌───────────────────┐  HTTPS   ┌────────────────────────┐  HTTPS   ┌──────────────┐
 │ App de dibujo     │─────────►│ recibe contenido       │◄─────────│ despierta    │
 │ (móvil, web)      │          │ compone la imagen      │  GET     │ cada X min,  │
 │ Programas         │          │ (800×480, 6 colores)   │─────────►│ descarga,    │
 │ (tiempo, etc.)    │          │ la sirve al cuadro     │  imagen  │ pinta y duerme│
 └───────────────────┘          └────────────────────────┘          └──────────────┘
```

**Repo (D16):** `server/` (Node.js), `firmware/` (cuadro) y `web/` (app de dibujo para el móvil, primer cliente). `server/` y `firmware/` con su `.env` fuera del repo.

---

## 2. Cuadro: hardware (D5, D7, D30, D32, D38)

| Pieza | Producto | Datos |
|---|---|---|
| Placa | Seeed **XIAO ePaper Display Board EE04** (SKU 100075670) | XIAO ESP32-S3 Plus; conector de batería JST 2,0 mm con interruptor de encendido y cargador integrado (carga por USB-C); 1 botón de reset y 3 de usuario (KEY0 GPIO2, KEY1 GPIO3, KEY2 GPIO5, activos en bajo); USB-C para alimentar y flashear (el cable debe llevar datos); conectores FPC de 24 y 50 pines de 0,5 mm, elegidos con un puente |
| Panel | Seeed **7,3" Spectra 6** (SKU 100064541) | 800×480, seis colores, FPC de 50 pines |
| Antena | FPC 2,4 GHz de 2,9 dBi | Incluida con la placa (hoja de datos de Seeed), junto con adaptadores y cables FFC de 24 y 50 pines |
| Batería | LiPo de 1S (3,7 V), JST-PH 2,0 mm, con protección | No incluida; ver §10 y D39 |
| Caja | Impresa en 3D (D12) | Ver §8 |

Fuentes: [wiki de la EE04](https://wiki.seeedstudio.com/epaper_ee04) · [Openelab: placa](https://openelab.io/products/seeed-studio-xiao-epaper-ee04) · [kit DIY](https://openelab.io/products/seeed-studio-xiao-epaper-7-3-inch-diy-kit).

| Requisito del cuadro | Valor |
|---|---|
| Imagen | 800×480 a seis colores: negro, blanco, rojo, amarillo, verde y azul |
| Refresco | 12–25 s, sin parcial; ≥180 s entre refrescos y uno completo cada 24 h como mínimo ([manual Waveshare](https://www.waveshare.com/wiki/7.3inch_e-Paper_HAT_%28E%29_Manual)) |
| Memoria | Imagen a 4 bits = 192 KB; PSRAM del XIAO ESP32-S3 Plus (hay que activarla en el Arduino) |

**Software de la placa (según la wiki):** Arduino con la biblioteca Seeed GFX2 y la placa "XIAO ESP32S3 Plus"; SenseCraft HMI, ESPHome, Home Assistant, PlatformIO y EEZ Studio. No hay ejemplo de 7,3" en la ruta que cita la wiki (sí de 7,5" monocromo). El firmware abierto `esp32-photoframe` soporta la EE04 ([software existente](software-existente.md)).

---

## 3. Energía (D5, D33, D34, D35)

El cuadro duerme y despierta cada X minutos: conecta al wifi, consulta al servidor, y solo si la imagen cambió refresca la pantalla; después vuelve a dormir. El servidor no puede empujar nada: espera a que el cuadro despierte.

- Requisito: una carga a la semana (D35). Intervalo mínimo de 3 min (D34), que se mide y se adapta.
- Autonomía: el firmware existente afirma ~10 µA durmiendo; la wiki de Waveshare dice ≤1 mA. Con ≥3 min entre consultas y refresco solo si cambia la imagen, una semana es viable incluso a 1 mA *(estimación, ver [requisitos](compra/01-requisitos.md))*. **Hay que medirlo** en la placa elegida.
- Siempre encendido gasta ~40–80 mA (según `esp32-photoframe`).
- El intervalo (A6) y el mínimo de 180 s entre refrescos limitan cada cuánto se ve un cambio.

---

## 4. Wifi del cuadro sin táctil (D4, D30, A1)

El cuadro no lleva wifi precargado. Vías posibles, abiertas:

**(a) Portal cautivo.** El cuadro crea su propia red wifi (no la del router); el móvil se conecta y abre un menú en el navegador, normalmente en 192.168.4.1 *(verificar)*. Es el modo que usa el firmware de serie de PhotoPainter y `esp32-photoframe`.

**(b) Bluetooth.** *Unified Provisioning* de Espressif ([documentación](https://docs.espressif.com/projects/esp-idf/en/v5.2.1/esp32s3/api-reference/provisioning/provisioning.html)): el móvil envía la red y la contraseña por BLE, cifradas (Security 1: Curve25519 y AES-256-CTR; Security 2: SRP6a y AES-256-GCM). Necesita una app o Web Bluetooth.

**(c) `wifi.txt` en la tarjeta SD.** Lo soporta `esp32-photoframe`; exige ranura microSD.

**(d) Botones.** Para elegir entre redes guardadas; EE04 y E1002 tienen 3 botones de usuario.

La pantalla del cuadro puede mostrar el nombre de red y un código (o QR). Se guardan varias redes.

---

## 5. Servidor (D14, D20–D22, A3)

Programa en Node.js en el servidor de casa del usuario. Coste 0 €.

**Estructura:** un módulo escucha en un puerto de forma pasiva; cada conexión nueva se entrega a un módulo de sesión y el listener vuelve a escuchar (D21).

**Funciones:**
1. Recibir contenido de las fuentes (la app de dibujo, programas).
2. Componer una imagen de 800×480 reducida a los seis colores del panel (tramado).
3. Servirla al cuadro por HTTPS.

**Acceso (D22):** el cuadro y las fuentes envían el secreto; el servidor compara el hash del secreto con el de su `.env` (tiempo constante). Los `.env` quedan fuera del repo y el repo lleva un `.env.example` sin valores.

**Interfaz con el cuadro:** una URL que devuelve la imagen actual y un identificador de versión (por ejemplo, ETag). Si el cuadro ya tiene esa versión, no se refresca la pantalla. `esp32-photoframe` ya consulta una URL con una programación, pero hay que comprobar cómo pasa una contraseña ([software existente](software-existente.md)).

**Cómo llega internet al servidor de casa (A3), pendiente de verificar:**
- Redirección de puertos + DNS dinámico + proxy con TLS (p. ej. Caddy). Falla si el proveedor usa CGNAT.
- Túnel (p. ej. Cloudflare Tunnel): evita abrir puertos; condiciones del plan gratuito sin verificar.

El ESP32 necesita un certificado válido en el servidor para validar TLS (Let's Encrypt o similar).

**Futuro, sin decidir:** un entorno en el servidor para ejecutar programas que generen contenido. Pide aislar la ejecución y revisar la autenticación de D22.

---

## 6. Fuentes de contenido

**App de dibujo (`web/`) (D26, A2, A4, A8):** web en el móvil que dibuja en un lienzo con eventos de puntero, con menú desplegable de colores y fondo blanco, cuadrícula o negro. Como el cuadro tarda minutos en actualizarse (D31, D34), la app puede **enviar la imagen ya rasterizada** (PNG) cuando se termina, en lugar de trazos en directo; el servidor la reduce a los seis colores. Con seis tintas, la paleta se limita a ellas o el servidor reduce cada color al más cercano.

**Programas / widgets:** scripts en el servidor (por ejemplo el tiempo) que producen una imagen o un texto y lo entregan al servidor de contenido. El formato exacto del contenido está por definir.

El cuadro solo recibe la imagen final: no conoce las fuentes (D1).

---

## 7. Firmware del cuadro (D8, A7)

Ciclo: despertar → wifi → consultar la URL → si hay versión nueva, descargarla y pintarla → dormir. Reintento con espera creciente si falla.

Opciones de partida (A7):
- Usar tal cual [esp32-photoframe](https://github.com/aitjcize/esp32-photoframe) (MIT; ESP-IDF).
- Partir de él y modificarlo.
- Escribir uno propio mínimo sobre ESP-IDF.

Datos de cada uno en [04-software-existente.md](software-existente.md). Verificar la licencia de cualquier código de ejemplo del fabricante antes de reutilizarlo.

---

## 8. Caja (D12, D3)

Soporte impreso en 3D con ventana para el panel, alojamiento para la placa y la batería, y acceso al USB-C, al interruptor de batería y a los botones. Diseño paramétrico (OpenSCAD o CadQuery) en el repo, con los STL.

- **Datos disponibles:** modelo 3D de la placa en GrabCAD, esquema en PDF y zip de PCB, enlazados desde la [wiki de la EE04](https://wiki.seeedstudio.com/epaper_ee04).
- **Medidas (hojas de datos de Seeed):** placa 81 × 44 × 7 mm; módulo del panel 170,2 × 111,2 × 0,9 mm; zona activa 160 × 96 mm; paso de píxel 0,2 mm.
- **No encontrados:** posición de agujeros de montaje de la placa. No hay caja para esta combinación; el modelo más cercano es un [marco de MakerWorld para el panel de Waveshare de 7,3"](https://makerworld.com/models/1924771) (mismo contorno), pensado para otra placa.
- Hay que proteger el cable FPC del panel y apoyar el panel sin presionarlo.

---

## 9. Riesgos

| Riesgo | Mitigación |
|---|---|
| Autonomía distinta de la anunciada (10 µA frente a ≤1 mA) | Medir el consumo en la placa real |
| Calidad del color: seis tintas y tramado | Probar imágenes y dibujos antes de decidir el contenido |
| Retención de imagen si la misma imagen dura más de un día | Refrescar al menos una vez al día, aunque no cambie (datasheet de Seeed) |
| Refresco lento y límite de ≥180 s entre refrescos | Intervalo ≥ 3 min; actualizar solo si cambió la imagen |
| Falta de ejemplo de código para el 7,3" en la wiki | Partir de `esp32-photoframe`, que soporta la EE04, o de los ejemplos de Seeed GFX2 |
| Batería con la polaridad invertida respecto a la placa (reportado por un comprador) | Comprobar la polaridad con la placa antes de conectar |
| Servidor de casa inaccesible (CGNAT) | Túnel |
| Cambian precios o stock | Dos fuentes de compra; confirmar antes de pedir |

---

---

## 10. Qué falta para completar el cuadro

| Pieza | Estado | Qué hace falta |
|---|---|---|
| Batería | No incluida (D39) | Cualquier LiPo de 1S (3,7 V) con protección y JST-PH 2,0 mm, de dron o similar; comprobar la polaridad con la serigrafía de la placa; se compra fuera de Mouser; se resuelve más adelante |
| Caja | No incluida | Diseño 3D propio (D12) |
| Cable y cargador USB-C | Los tienes | Cable con datos |
| Firmware | No escrito (D18) | Partir de `esp32-photoframe` o de Seeed GFX2 |
| Wifi sin táctil | Abierto (A1) | Tres botones de usuario, portal cautivo, BLE |
| Servidor y fuentes | Plan 4 en pausa | Se retoma después de la compra |

---

## Fuentes (octubre de 2026)

- [Guía de compra](compra/README.md) y sus registros.
- [Espressif — Unified Provisioning](https://docs.espressif.com/projects/esp-idf/en/v5.2.1/esp32s3/api-reference/provisioning/provisioning.html)
- [Waveshare — manual 7,3" HAT (E)](https://www.waveshare.com/wiki/7.3inch_e-Paper_HAT_%28E%29_Manual)

---
