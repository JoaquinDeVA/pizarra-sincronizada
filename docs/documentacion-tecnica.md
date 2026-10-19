# Documentación técnica — Pizarra sincronizada

Táctica: **cómo** se materializan las decisiones de [decisiones-de-diseno.md](decisiones-de-diseno.md) (se citan como D1…, A1…). Aquí no se discute el porqué.

Fecha: 2026-10-23

> Precios: capturas de octubre de 2026. Cambian a menudo; confirma antes de comprar. *(estimación)* = cifra mía, sin fuente.

---

## 1. Visión general

```
  Casa A                       Casa B
 ┌──────────────┐  wss  ┌────────────┐  wss  ┌────────────────────┐
 │ Móvil        │◄─────►│  SERVIDOR  │◄─────►│ Cuadro ESP32       │
 │ cliente web  │       │ (casa del  │       │ solo muestra       │
 │ (dibuja)     │       │  usuario)  │       │ (caja 3D, USB-C)   │
 └──────────────┘       └────────────┘       └────────────────────┘
```

**Repo (D16):** `server/` (Node.js), `web/` (cliente de dibujo para el móvil) y `firmware/` (cuadro). `server/` y `firmware/` con su `.env` fuera del repo. La caja (D12) es una pieza aparte.

---

## 2. Cuadro: hardware (D7, D29, D30, A5)

Placa ESP32 ya montada con pantalla **sin táctil**, comprada hecha. Un solo cuadro (D28). Tecnología y tamaño abiertos (A5).

| Requisito | Valor |
|---|---|
| SoC | ESP32-S3 (LCD RGB) o ESP32 con driver de e-paper |
| Pantalla | Sin táctil; LCD o e-paper; tamaño por decidir |
| Conectividad | Wifi 2,4 GHz |
| Alimentación | USB-C a 5 V constante (D5) |
| Memoria | Lienzo en PSRAM si hay (800×480×2 B ≈ 750 KB en RGB565); ver [requisitos](compra/00-requisitos.md) |

Datos de LCD y de e-paper, y las placas candidatas, en [compra/](compra/README.md): [modelos](compra/01-modelos.md), [vendedores e impuestos](compra/02-vendedores.md), [tecnologías](compra/03-tecnologias-de-pantalla.md). Esas fichas se hicieron para placas con táctil: los datos de pantalla y memoria valen; falta investigar variantes sin táctil.

---

## 3. Alimentación (D5)

USB-C constante, cargador de ≥2 A para la LCD de 7" (Waveshare: 450 mA a 5 V según su [wiki](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7)). Un e-paper consume solo al refrescar. Sin batería.

---

## 4. Wifi del cuadro (D4, D30, A1)

El cuadro no lleva wifi precargado y no tiene táctil: la red se da desde el móvil. Dos opciones, abiertas:

**(a) Bluetooth.** Espressif ofrece *Unified Provisioning* ([documentación](https://docs.espressif.com/projects/esp-idf/en/v5.2.1/esp32s3/api-reference/provisioning/provisioning.html)): el móvil envía la red y la contraseña por BLE, cifradas.
- *Security 1:* Curve25519 y AES-256-CTR, con clave de autorización opcional (PoP).
- *Security 2:* SRP6a y AES-256-GCM; credenciales preparadas con una pegatina QR.
- Necesita una app o web con BLE (Web Bluetooth) en el móvil: sin verificar disponibilidad ni licencia de la app de Espressif.

**(b) Portal cautivo.** El cuadro crea su propia red wifi (no la del router); el móvil se conecta y abre el menú en el navegador, normalmente en 192.168.4.1 *(verificar)*. La contraseña viaja cifrada si la red del cuadro es WPA2.

La pantalla del cuadro puede mostrar el nombre de red y un código (o QR) para demostrar presencia física. Se guardan varias redes.

---

## 5. Servidor (D14, D20–D24, A3)

Programa en Node.js con [`ws`](https://github.com/websockets/ws) (MIT), en el servidor de casa del usuario. Coste 0 €.

**Estructura:** un módulo escucha en un puerto de forma pasiva; cada conexión nueva se entrega a un módulo de sesión y el listener vuelve a escuchar (D21). Si el cliente web se sirve desde el servidor (A2a), el mismo listener entrega los ficheros estáticos por HTTP.

**Acceso (D22):**
- El primer mensaje es `auth` con el secreto y el rol. El servidor compara el hash del secreto con el de su `.env` (tiempo constante).
- Sin autenticar en pocos segundos: se cierra. Intentos fallidos limitados por IP.
- Los `.env` quedan fuera del repo; el repo lleva un `.env.example` sin valores.

**Sala (D23, D24, D28):** una sola, con un dibujante y un espectador. Solo el dibujante puede enviar trazos; el servidor ignora los del espectador. El servidor guarda en memoria el registro de trazos y lo reenvía al reconectar. Si un cliente vuelve con una conexión antigua abierta, la antigua se sustituye.

**Cómo llega internet al servidor de casa (A3), pendiente de verificar:**
- Redirección de puertos + DNS dinámico + proxy con TLS (p. ej. Caddy). Falla si el proveedor usa CGNAT.
- Túnel (p. ej. Cloudflare Tunnel): evita abrir puertos; condiciones del plan gratuito sin verificar.

El ESP32 necesita un certificado válido en el servidor para validar TLS (Let's Encrypt o similar). El navegador del móvil también exige HTTPS para una PWA.

---

## 6. Protocolo (borrador)

JSON por WebSocket (wss). Coordenadas en un lienzo lógico fijo; cada extremo escala a su pantalla.

```
Cliente → servidor
  {"t":"auth","secret":"<secreto del .env>","role":"draw"|"view"}   // primer mensaje
  {"t":"b","id":17,"c":"#e53935","w":6}        // empieza trazo (solo "draw")
  {"t":"p","id":17,"pts":[[x,y],[x,y],...]}   // puntos del trazo
  {"t":"e","id":17}                             // fin de trazo
  {"t":"clear"}                                 // borrar todo
  {"t":"cfg","brightness":0-100,"sleep":minutos} // ajustes del cuadro (solo "draw")
Servidor → cliente
  {"t":"ok"} | {"t":"peer","on":true|false}    // autenticado / el otro extremo entra o sale
  {"t":"snap","log":[...]}                      // al entrar: registro desde el último clear
  (reenvía b/p/e/clear/cfg en orden)
```

El servidor numera los eventos y guarda el registro, así que quien se reconecta recibe `snap`. El dibujante pinta en local al instante y luego envía, con puntos agrupados a ~30 Hz. Colores, goma y deshacer dependen de A4.

---

## 7. Cliente de dibujo (`web/`) (D15, D26, A2, A4)

Pantalla completa en el móvil: lienzo, menú desplegable de colores, fondo blanco/cuadrícula/negro (D26), y ajustes del cuadro (brillo, apagado). Es el sitio donde se juega la calidad del dibujo (D15):

| Factor | Qué se hace |
|---|---|
| Entrada | Eventos de puntero (`pointer events`) con puntos intermedios agrupados; sin el gesto de desplazar de la página |
| Camino al píxel | El trazo se pinta en local sin esperar a la red |
| Forma del trazo | Interpolar entre puntos (curva suave) |
| Temblor de pulso | Filtro de suavizado con poca latencia (p. ej. [filtro 1€](https://gery.casiez.net/1euro/)) |
| Conexión | Reconexión automática; muestra si el cuadro está conectado |
| Instalación | PWA si se elige A2a; el secreto se pide la primera vez y se guarda en el móvil |

Lo que se medirá: latencia local (dedo→píxel), latencia hasta el cuadro, suavidad de línea y pérdida de puntos en trazos rápidos. Los valores objetivo quedan por fijar.

---

## 8. Firmware del cuadro (D30)

Recibe y pinta; no lee táctil ni tiene menús. Pasos: conectar al wifi guardado, abrir wss, autenticar como `view`, aplicar `snap` y pintar los trazos que llegan. Ajustes de brillo y apagado por inactividad recibidos por `cfg`.

| Pieza | Elección | Licencia |
|---|---|---|
| Entorno | PlatformIO + Arduino-ESP32 | Apache 2.0 / LGPL 2.1 |
| Gráficos | Por decidir con A5 (LCD: dibujo directo o LVGL; e-paper: la librería del panel) | — |
| WebSocket | `esp_websocket_client` o [arduinoWebSockets](https://github.com/Links2004/arduinoWebSockets) | Apache 2.0 / LGPL 2.1 |
| Wifi | Según A1 | — |
| TLS | mbedTLS incluido | Apache 2.0 |

- Si el cuadro es e-paper, el parcial tarda 0,4–1,5 s y deja imagen residual; una guía recomienda un refresco completo cada 5–10 parciales *(fuente secundaria, ver [tecnologías](compra/03-tecnologias-de-pantalla.md))*. Para solo mirar es aceptable; hay que agrupar trazos en cada refresco.
- Reconexión automática con espera creciente.
- Verificar la licencia de cualquier driver o ejemplo del fabricante antes de reutilizarlo.

---

## 9. Caja (D12, D3)

Soporte para escritorio con hueco para el USB-C. Diseño paramétrico (OpenSCAD o CadQuery) en el repo, con los STL publicados. Pendiente: medidas de la placa elegida.

---

## 10. Riesgos

| Riesgo | Mitigación |
|---|---|
| Dibujo en el móvil con retraso o a saltos | Medir antes de comprar; pintado local, suavizado |
| E-paper con imagen residual o lag | Refresco completo periódico; solo se mira, no se dibuja |
| Servidor de casa inaccesible (CGNAT) | Túnel |
| Revisión de placa distinta a la esperada | Anotar revisión al comprar |
| Cambian precios o stock | Dos fuentes de compra; confirmar antes de pedir |

---

## Fuentes (octubre de 2026)

- [Espressif — Unified Provisioning](https://docs.espressif.com/projects/esp-idf/en/v5.2.1/esp32s3/api-reference/provisioning/provisioning.html) · [FAQ de interfaces LCD](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html)
- [Waveshare wiki ESP32-S3-Touch-LCD-7](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7)
- [Filtro 1€](https://gery.casiez.net/1euro/)
