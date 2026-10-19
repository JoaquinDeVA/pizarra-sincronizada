# Decisiones de diseño — Cuadro e-ink

Estrategia: **qué** queremos y **por qué**. Cómo se lleva a la realidad vive en [documentacion-tecnica.md](documentacion-tecnica.md) y la guía de compra en [compra/](compra/README.md). Se citan por código (D1, A1…).

Fecha: 2026-10-29

Estados: **Decidida** (la tomó el usuario; absoluta salvo que se argumente un error) · **Abierta** (sin decidir; se listan opciones, sin recomendar).

---

## 1. Propósito

Una pantalla e-ink a color sobre el escritorio que solo muestra lo que ocurre en otro lado: imágenes, widgets (por ejemplo el tiempo), dibujos hechos desde el móvil o programas. El servidor hace las cosas y manda actualizaciones cada cierto tiempo. El cuadro es un objeto nuevo y separado del móvil.

## 2. Decisiones tomadas

| Cód. | Decisión | Por qué |
|---|---|---|
| D1 | **Hay una sola pantalla,** que solo representa lo que ocurre en otro lado. El otro extremo puede ser una app para dibujar en el móvil o programas que muestran cosas | El cuadro es un visor |
| D2 | **Quien tiene el cuadro es poco técnico** (puede tener ayuda puntual): se enciende y funciona; lo único que se configura es el wifi. **Quien está al otro extremo es muy técnico** (informático, desarrolla con Claude) y hace las cosas desde el servidor | La complejidad vive en el servidor, no en el cuadro |
| D3 | **El cuadro se mueve de sitio pero no es robusto:** uso en casa u oficina, sin resistencia al agua ni entornos agresivos | Uso doméstico |
| D4 | **Conexión wifi modificable:** el cuadro se conecta al wifi del sitio donde esté, sin precargar ninguna red | No se sabe dónde estará en el futuro |
| D5 | **Con batería:** el cuadro duerme y despierta cada X minutos para pedir la imagen al servidor; el servidor no empuja | Autonomía de semanas o meses |
| D6 | **Presupuesto: hasta ~100 €** por la pantalla y el hardware necesario | El e-ink de color cuesta más que el objetivo inicial |
| D7 | **Plataforma ESP32.** Se compra la pantalla más el hardware necesario para que funcione, sea un kit montado o piezas sueltas, **sin que sea un producto terminado cerrado:** tiene que dar acceso a programadores terceros | Se quiere programarlo |
| D8 | **Sin software de pago ni que pueda pasar a serlo.** El firmware lo escribimos nosotros, sencillo, pudiendo partir de código ya existente | Evitar licencias o cuotas futuras |
| D9 | **Proyecto compartible:** repositorio en GitHub, licencia MIT | El usuario quiere compartirlo |
| D12 | **Caja o soporte impreso en 3D, dentro del alcance** si el hardware no trae | Es el objeto de escritorio |
| D14 | **Servidor en el servidor de casa del usuario,** que recibe el contenido y lo entrega al cuadro | Evita servidor de pago |
| D16 | **El repo tiene dos partes en directorios distintos:** `server/` y `firmware/`, cada uno con su `.env` fuera del repo | Son programas distintos |
| D18 | **Primero la pantalla:** el software se desarrolla después y no hace falta medir capacidades antes de comprar | Ahora el foco es el hardware |
| D20 | **Servidor en Node.js** | Un archivo pequeño y librerías maduras |
| D21 | **El servidor escucha de forma pasiva en un puerto;** al llegar una conexión la delega a otro módulo y vuelve a escuchar | Separa la recepción de la gestión |
| D22 | **Solo entra quien tiene la contraseña:** el servidor guarda su hash en su `.env`; el cuadro y los clientes llevan el mismo secreto. Los `.env` quedan sin subir al repo | Solo nuestro software opera ahí |
| D29 | **El cuadro es un objeto nuevo y dedicado,** no un dispositivo ya existente (tablet, tele, Raspberry) | Separación mental entre el móvil y estas notas |
| D30 | **El cuadro no tiene táctil ni menús:** solo muestra | Solo se mira |
| D31 | **El refresco lento del cuadro es aceptable:** no hace falta tiempo real | El cuadro no es donde se dibuja |
| D32 | **La pantalla es e-ink y a color** (brillo sano, batería, calidad de imagen) | Preferencia del usuario |
| D33 | **El cuadro consulta al servidor cada X tiempo,** o se registra en el servidor y se conecta después; el servidor manda actualizaciones | Encaja con dormir entre consultas |
| D34 | **El intervalo de consulta no baja de 3 minutos;** se mide y se adapta | Menos sería innecesario |
| D35 | **Autonomía: una carga a la semana basta;** más es mejor | Es el uso previsto |
| D36 | **Se acepta el color de seis tintas** (negro, blanco, rojo, amarillo, verde y azul), con imágenes tramadas | Es lo que ofrece el e-ink de color |
| D37 | **Criterio principal: la mejor pantalla posible** | El software puede adaptarse después |
| D38 | **Hardware: placa Seeed XIAO ePaper Display Board EE04** (SKU 100075670, XIAO ESP32-S3 Plus) **más panel Seeed de 7,3" Spectra 6, 800×480** (SKU 100064541), comprados por separado o como el kit DIY, por precio. Se completa con una batería y una caja | Es la opción que cumple color, 7,3", ESP32, apertura de desarrollo y presupuesto (D6) |
| D39 | **Batería: cualquier LiPo de una celda (3,7 V) con protección y conector JST-PH 2,0 mm,** de dron o similar; se resuelve más adelante. Las de dron suelen ser de varias celdas (7,4 V o más) y con conector XT60: no sirven sin ser de 1S y sin cambiar el conector | Cualquiera vale si cumple 3,7 V y el conector |

## 3. Fuera de alcance (por ahora)

Vídeo o voz, cuentas de usuario, historial de dibujos, imágenes en tiempo real, resistencia al agua, 4G, montaje de hardware propio con PCB, dibujar desde el cuadro, más de un cuadro. **Futuro, sin decidir:** un entorno en el servidor para ejecutar programas online que generen lo que muestra el cuadro.

## 4. Anti-objetivos (fracasaría aunque funcione)

- Que exija configuración técnica a quien mira el cuadro.
- Que dependa de un servicio que pueda empezar a cobrar o cambiar sus condiciones.
- Que el hardware sea un producto cerrado al que no se pueda poner firmware propio.

## 5. Decisiones abiertas

Cada una lista opciones, sin recomendar una; los datos están en la documentación técnica y en la guía de compra.

| Cód. | Pregunta | Opciones |
|---|---|---|
| A1 | **Cómo se da el wifi al cuadro sin táctil** (D4, D30) | (a) Portal cautivo: el cuadro crea su red y el menú se abre en el móvil; (b) Bluetooth con cifrado; (c) fichero `wifi.txt` en la tarjeta SD; (d) botones de la placa |
| A3 | **Cómo llega internet al servidor de casa** (D14) | Redirección de puertos + DNS dinámico, o túnel |
| A5 | **Dónde comprar la placa y el panel de D38** | Mouser (sin stock; plazo por presupuesto), Openelab (UE, preventa), o el kit DIY de Openelab |
| A7 | **Firmware** (D8) | (a) Usar tal cual el firmware abierto existente (`esp32-photoframe`, MIT); (b) partir de él y modificarlo; (c) escribir uno propio mínimo |
