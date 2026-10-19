# Decisiones de diseño — Pizarra sincronizada

Estrategia: **qué** queremos y **por qué**. Cómo se lleva a la realidad (hardware, compras, protocolo, firmware) vive en [documentacion-tecnica.md](documentacion-tecnica.md), que cita estas decisiones por su código (D1, D2…).

Fecha: 2026-10-19

Estados: **Decidida** (la tomó el usuario; absoluta salvo que se argumente un error) · **Abierta** (sin decidir; se listan las opciones, no una propuesta).

---

## 1. Propósito

Dos aparatos, cada uno en una casa. Una persona dibuja o borra y la otra lo ve al instante. Nada más.

**El proyecto está centrado en la calidad de la experiencia táctil.** No cumple ninguna otra función, así que lo único que importa es que dibujar se sienta bien.

## 2. Decisiones tomadas

| Cód. | Decisión | Por qué |
|---|---|---|
| D1 | **Uso remoto:** cada aparato en una casa distinta, conectados por internet | Es el caso de uso |
| D2 | **Para personas poco técnicas:** se enciende y funciona; lo único que se configura es el wifi | El usuario final no es técnico |
| D3 | **Se mueve de sitio pero no es robusto:** uso en casa u oficina, sin resistencia al agua ni entornos agresivos | Uso doméstico |
| D4 | **Conexión wifi modificable:** el aparato se conecta al wifi del sitio donde esté, sin precargar ninguna red | No se sabe dónde estará en el futuro |
| D5 | **Alimentación por USB-C constante, sin batería** | Ahorra coste y complejidad |
| D6 | **Presupuesto: ~20 € por aparato, "un poco más" admisible** | Proyecto barato |
| D7 | **Placas ya montadas con ESP32 y pantalla integrados**, compradas hechas; no se monta hardware propio | Menos riesgo y trabajo; el esfuerzo va al software |
| D8 | **Sin software de pago ni que pueda pasar a serlo;** el código lo escribimos nosotros | Evitar licencias o cuotas futuras |
| D9 | **Proyecto compartible:** repositorio en GitHub, licencia MIT | El usuario quiere compartirlo |
| D10 | **Pantalla táctil capacitiva.** Tamaños en consideración: 4,3" y 7" | Hay que contrastar precio y resultado |
| D11 | **El lápiz es un módulo independiente:** se compra aparte y puede ignorarse en el desarrollo | Reduce el alcance |
| D12 | **Caja impresa en 3D, dentro del alcance** (el usuario tiene impresora) | Abarata y adapta la caja |
| D13 | **Compra desde dos fuentes:** AliExpress/eBay (barato, lento) y tiendas UE (rápido, más caro) | Elegir según precio y plazo |
| D14 | **Servidor intermedio entre los dos aparatos, en el servidor que el usuario ya tiene en casa** | Evita servidor de pago y conexión directa entre aparatos |
| D15 | **La calidad táctil es el criterio principal** de cualquier elección técnica | Es la única función del proyecto |
| D16 | **El repo tiene dos partes:** el código de las pizarras (firmware) y el del servidor | Son dos programas distintos |

## 3. Fuera de alcance

Vídeo o voz, cuentas de usuario, historial de dibujos, más de 2 aparatos, imágenes importadas, resistencia al agua, batería, 4G, montaje de hardware propio, lápiz con presión o rechazo de palma.

## 4. Anti-objetivos (fracasaría aunque funcione)

- Que dibujar se note con retraso, a saltos o impreciso.
- Que exija configuración técnica para el uso diario.
- Que dependa de un servicio que pueda empezar a cobrar o cambiar sus condiciones.

## 5. Decisiones abiertas

Cada una lista opciones, sin recomendar una; los datos están en la documentación técnica.

| Cód. | Pregunta | Opciones |
|---|---|---|
| A1 | **Cómo se da el wifi al aparato** (D4) | (a) Bluetooth desde el móvil con cifrado; (b) portal cautivo: el aparato crea su propia red y el menú se abre desde el móvil; (c) ambas |
| A2 | **Librería gráfica y táctil** (D15) | LovyanGFX o LVGL; se decide midiendo la latencia en el prototipo |
| A3 | **Cómo llega internet al servidor de casa** (D14) | Redirección de puertos + DNS dinámico, o túnel |
| A4 | **Funciones de dibujo** | Colores, grosores, goma por trazo o por píxel, deshacer |
| A5 | **Tamaño y modelo de placa** (D10) | Dos placas de 4,3", dos de 7", o una de cada para comparar |
| A6 | **Mismo modelo en ambas casas o modelos distintos** | Mismo modelo; distintos con bandas negras |
