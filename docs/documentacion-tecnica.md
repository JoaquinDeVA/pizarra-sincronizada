# Documentación técnica — Cuadro e-ink

Táctica: **cómo** se materializan las decisiones de [decisiones-de-diseno.md](decisiones-de-diseno.md) (se citan como D1…, A1…). Aquí no se discute el porqué.

Fecha: 2026-10-24

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

## 2. Cuadro: hardware (D5, D7, D30, D32, A5)

Pantalla e-ink a color de 7,3" (Spectra 6, 800×480), ESP32-S3, con batería, sin táctil, y con acceso de programación. Tres kits candidatos en [compra/02-modelos.md](compra/02-modelos.md): Waveshare PhotoPainter, Seeed XIAO EE04 y Seeed reTerminal E1002.

| Requisito | Valor |
|---|---|
| Imagen | 800×480 a seis colores: negro, blanco, rojo, amarillo, verde y azul |
| Refresco | 12–25 s, sin parcial; ≥180 s entre refrescos y uno completo cada 24 h como mínimo ([manual Waveshare](https://www.waveshare.com/wiki/7.3inch_e-Paper_HAT_%28E%29_Manual)) |
| Energía | Batería de 3,7 V con cargador integrado; sueño profundo; despertar por RTC |
| Memoria | Imagen a 4 bits = 192 KB; las placas traen 8 MB de PSRAM (ver [requisitos](compra/01-requisitos.md)) |

---

## 3. Energía (D5, D33, A6)

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

**Interfaz con el cuadro:** una URL que devuelve la imagen actual y un identificador de versión (por ejemplo, ETag). Si el cuadro ya tiene esa versión, no se refresca la pantalla. `esp32-photoframe` ya consulta una URL con una programación, pero hay que comprobar cómo pasa una contraseña ([software existente](compra/04-software-existente.md)).

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

Datos de cada uno en [04-software-existente.md](compra/04-software-existente.md). Verificar la licencia de cualquier código de ejemplo del fabricante antes de reutilizarlo.

---

## 8. Caja (D12, D3)

Si el kit no trae caja (EE04): soporte impreso en 3D con hueco para el USB-C, la batería y los botones. Diseño paramétrico (OpenSCAD o CadQuery) en el repo, con los STL. Pendiente: medidas de la placa elegida.

---

## 9. Riesgos

| Riesgo | Mitigación |
|---|---|
| Autonomía distinta de la anunciada (10 µA frente a ≤1 mA) | Medir el consumo en la placa real |
| Calidad del color: seis tintas y tramado | Probar imágenes y dibujos antes de decidir el contenido |
| Refresco lento y límite de ≥180 s entre refrescos | Intervalo ≥ 3 min; actualizar solo si cambió la imagen |
| Kit con firmware cerrado o difícil de reflashear | Comprobar el USB-C y el modo de descarga al recibir la placa |
| EE04 en preventa sin ejemplo oficial para el 7,3" | Partir de `esp32-photoframe`, que la soporta |
| Servidor de casa inaccesible (CGNAT) | Túnel |
| Cambian precios o stock | Dos fuentes de compra; confirmar antes de pedir |

---

## Fuentes (octubre de 2026)

- [Guía de compra](compra/README.md) y sus registros.
- [Espressif — Unified Provisioning](https://docs.espressif.com/projects/esp-idf/en/v5.2.1/esp32s3/api-reference/provisioning/provisioning.html)
- [Waveshare — manual 7,3" HAT (E)](https://www.waveshare.com/wiki/7.3inch_e-Paper_HAT_%28E%29_Manual)
