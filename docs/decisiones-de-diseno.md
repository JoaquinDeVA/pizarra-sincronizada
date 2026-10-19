# Decisiones de diseño — Pizarra sincronizada

Estrategia: **qué** queremos y **por qué**. Cómo se lleva a la realidad vive en [documentacion-tecnica.md](documentacion-tecnica.md) y la guía de compra en [compra/](compra/README.md). Se citan por código (D1, A1…).

Fecha: 2026-10-23

Estados: **Decidida** (la tomó el usuario; absoluta salvo que se argumente un error) · **Abierta** (sin decidir; se listan opciones, sin recomendar).

---

## 1. Propósito

Una persona dibuja desde su móvil y la otra lo ve al instante en un cuadro dedicado, sobre su escritorio, en otra casa. El cuadro es un objeto nuevo y separado del móvil: la separación entre el móvil y estas notas es intencional.

## 2. Decisiones tomadas

| Cód. | Decisión | Por qué |
|---|---|---|
| D1 | **Uso remoto:** una persona dibuja desde el móvil y la otra mira en el cuadro, en casas distintas, conectadas por internet | Es el caso de uso |
| D2 | **El cuadro es para una persona poco técnica:** se enciende y funciona; lo único que se configura es el wifi | El usuario final no es técnico |
| D3 | **El cuadro se mueve de sitio pero no es robusto:** uso en casa u oficina, sin resistencia al agua ni entornos agresivos | Uso doméstico |
| D4 | **Conexión wifi modificable:** el cuadro se conecta al wifi del sitio donde esté, sin precargar ninguna red | No se sabe dónde estará en el futuro |
| D5 | **Alimentación por USB-C constante, sin batería** | Ahorra coste y complejidad |
| D6 | **Presupuesto: ~50 € en total, "o algo más"** | Proyecto barato |
| D7 | **Plataforma ESP32,** con pantalla ya montada y comprada hecha; no se monta hardware propio | Menos riesgo; el esfuerzo va al software |
| D8 | **Sin software de pago ni que pueda pasar a serlo;** el código lo escribimos nosotros | Evitar licencias o cuotas futuras |
| D9 | **Proyecto compartible:** repositorio en GitHub, licencia MIT | El usuario quiere compartirlo |
| D12 | **Caja o soporte impreso en 3D, dentro del alcance** (el usuario tiene impresora) | Es el objeto de escritorio |
| D13 | **Compra desde dos fuentes:** AliExpress/eBay (barato, lento) y tiendas UE (rápido, más caro) | Elegir según precio y plazo |
| D14 | **Servidor intermedio entre el móvil y el cuadro, en el servidor de casa del usuario** | Evita servidor de pago y conexión directa |
| D15 | **Criterio principal: la calidad de la experiencia de dibujo en el móvil** | Es la única función del proyecto |
| D16 | **El repo tiene tres partes en directorios distintos:** `server/`, `web/` (cliente de dibujo para el móvil) y `firmware/` (cuadro). `server/` y `firmware/` llevan su `.env` fuera del repo | Son programas distintos |
| D18 | **Se empieza por el software, sin hardware** | Medir antes de comprar |
| D19 | **El cuadro se compra cuando el software esté medido** | Evita comprar con datos a medias |
| D20 | **Servidor en Node.js,** en el servidor de casa | Un archivo pequeño y librería WebSocket madura |
| D21 | **El servidor escucha de forma pasiva en un puerto;** al llegar una conexión la delega a otro módulo y vuelve a escuchar | Separa la recepción de la gestión de sesiones |
| D22 | **Solo entra quien tiene la contraseña:** el servidor guarda su hash en su `.env`; el cuadro y el cliente de dibujo llevan el mismo secreto. Los `.env` quedan sin subir al repo | Solo nuestro software opera ahí; el secreto se fija al desplegar |
| D23 | **Una sola sala fija** con dos roles: el que dibuja y el que mira. El servidor es su punto de reunión | Es un único par |
| D24 | **El servidor guarda el dibujo en memoria** y lo reenvía al reconectar; se pierde si el servidor se reinicia | Basta para el uso previsto |
| D26 | **Fondo blanco, cuadrícula o negro, y colores en menú desplegable,** en el cliente de dibujo | Cubre lo pedido para el dibujo |
| D28 | **Roles fijos:** una persona solo dibuja y la otra solo mira. Se compra **un solo cuadro** | Es el uso previsto |
| D29 | **El cuadro es un objeto nuevo y dedicado,** no un dispositivo ya existente (tablet, tele, Raspberry) | Separación mental entre el móvil y estas notas |
| D30 | **El cuadro no tiene táctil ni menús:** solo muestra. Sus ajustes (wifi, brillo, apagado) se hacen desde el móvil | El dibujo ocurre en el móvil |
| D31 | **El refresco lento del cuadro es aceptable:** no hace falta tiempo real, porque son dibujos y se dibuja en el móvil. Se vuelven a valorar los e-paper lentos, incluidos los de color | El cuadro no es donde se dibuja |

## 3. Fuera de alcance

Vídeo o voz, cuentas de usuario, historial de dibujos, más de 2 personas, imágenes importadas, resistencia al agua, batería, 4G, montaje de hardware propio, dibujar desde el cuadro, más de un cuadro.

## 4. Anti-objetivos (fracasaría aunque funcione)

- Que dibujar en el móvil se note con retraso, a saltos o impreciso.
- Que exija configuración técnica a quien mira el cuadro.
- Que dependa de un servicio que pueda empezar a cobrar o cambiar sus condiciones.

## 5. Decisiones abiertas

Cada una lista opciones, sin recomendar una; los datos están en la documentación técnica y en la guía de compra.

| Cód. | Pregunta | Opciones |
|---|---|---|
| A1 | **Cómo se da el wifi al cuadro desde el móvil** (D4, D30) | (a) Bluetooth con cifrado; (b) portal cautivo: el cuadro crea su red y el menú se abre en el navegador del móvil |
| A2 | **Cliente de dibujo en el móvil** | (a) Web (PWA) servida por el servidor, sin tienda de apps; (b) app nativa |
| A3 | **Cómo llega internet al servidor de casa** (D14) | Redirección de puertos + DNS dinámico, o túnel |
| A4 | **Herramientas de dibujo** | Grosores, goma, borrar todo, deshacer, paleta de colores, texto |
| A5 | **Tecnología y tamaño del cuadro** (D7, D30, D31) | LCD sin táctil (brillante, refresco rápido); e-paper B/N (parcial 0,4–1,5 s); e-paper de color de 6 colores (refresco de 12–25 s, sin parcial); tamaño por decidir. Detalle en [compra/](compra/README.md) |
| A8 | **Cuándo y cómo se actualiza el cuadro** (D31) | (a) En cuanto llegan trazos, con un mínimo entre refrescos; (b) cuando quien dibuja termina o pulsa enviar; (c) cada cierto tiempo fijo. Y quién compone la imagen: el servidor (envía un bitmap ya reducido a los colores del panel) o el cuadro (recibe trazos y los pinta) |
| A9 | **Colores con un e-paper de color** (D26) | Paleta del cliente limitada a los colores del panel (Spectra 6: negro, blanco, rojo, amarillo, verde y azul); o libre, con el servidor reduciendo cada color al más cercano |
| A6 | **Fondo:** ¿lo elige quien dibuja y se ve igual en el cuadro, o es un ajuste del cuadro desde el móvil? (D26) | Compartido; propio del cuadro |
| A7 | **Forma del lienzo** | Misma proporción que la pantalla del cuadro, con bandas si el móvil difiere; o lienzo mayor que se ve entero escalado |
