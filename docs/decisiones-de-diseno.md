# Decisiones de diseño — Pizarra sincronizada

Estrategia: **qué** queremos y **por qué**. Cómo se lleva a la realidad (hardware, protocolo, firmware, compras) vive en [documentacion-tecnica.md](documentacion-tecnica.md), que cita estas decisiones por su código (D1, D2…).

Fecha: 2026-10-16

Estados: **Decidida** (la tomó el usuario) · **Propuesta** (propuesta de Claude, pendiente de ratificar) · **Abierta** (sin decidir).

---

## 1. Propósito

Dos aparatos, cada uno en una casa. Una persona dibuja o borra y la otra lo ve al instante. Nada más.

## 2. Decisiones del usuario

| Cód. | Decisión | Por qué |
|---|---|---|
| D1 | **Uso remoto:** cada aparato en una casa distinta, conectados por internet | Es el caso de uso |
| D2 | **Para personas poco técnicas:** se enciende y funciona; lo único que se configura es el wifi | El usuario final no es técnico |
| D3 | **Portátil pero no robusto:** se mueve de sitio, se usa en casa/oficina y en el coche; no hace falta resistencia al agua ni a entornos agresivos | Uso doméstico, sin condiciones extremas |
| D4 | **Conexión modificable:** la red cambia (casa, oficina, hotspot del móvil) | Se mueve entre sitios |
| D5 | **Alimentación solo por USB o powerbank, sin batería propia** | Ahorra coste y complejidad |
| D6 | **Presupuesto: ~20 € por aparato, "un poco más" admisible** | Proyecto barato; el primer diseño (~60–130 €) se descartó |
| D7 | **Todo integrado en uno:** un único módulo con pantalla, táctil, procesador y wifi | Menos piezas y menos montaje |
| D8 | **Plataforma ESP32** | Barato y suficiente: solo se pinta con colores |
| D9 | **Sin software de pago ni que pueda pasar a serlo;** el código lo escribimos nosotros | Evitar licencias o cuotas futuras |
| D10 | **Código y proyecto compartibles** (repositorio en GitHub, licencia MIT) | El usuario quiere compartir el proyecto |
| D11 | **Pantalla táctil capacitiva, tamaño entre 4,3" y 7":** ambos candidatos se mantienen abiertos hasta probar | Hay que contrastar precio real y resultado |
| D12 | **El lápiz es un módulo independiente:** se compra aparte y puede ignorarse en el desarrollo | Reduce el alcance |
| D13 | **Caja impresa en 3D, dentro del alcance** (el usuario tiene impresora) | Abarata y adapta la caja |
| D14 | **Compra desde dos fuentes:** AliExpress/eBay (barato, lento) y tiendas UE (rápido, más caro) | Elegir según precio y plazo |

## 3. Fuera de alcance (non-goals)

Vídeo o voz, cuentas de usuario, historial de dibujos, más de 2 aparatos, imágenes importadas, resistencia al agua, batería integrada, lápiz con presión o rechazo de palma.

## 4. Anti-objetivos (fracasaría aunque funcione)

- Que exija configuración técnica para uso diario.
- Que dependa de un servicio que pueda empezar a cobrar o cambiar sus condiciones.
- Que el coste por aparato se acerque al de una tablet barata.

## 5. Propuestas de Claude (pendientes de ratificar)

| Cód. | Propuesta | Por qué |
|---|---|---|
| P1 | **Servidor intermedio (relay) en internet** en lugar de conexión directa entre aparatos | La conexión directa falla tras NAT/CGNAT de datos móviles y obligaría a un servidor TURN |
| P2 | **Orden total de eventos fijado por el relay, sin CRDT** | Con 2 personas y un servidor central basta |
| P3 | **Goma = trazo del color del fondo; "borrar todo" vacía el registro** | Resultado idéntico en ambos lados y sin recalcular el lienzo |
| P4 | **El aparato crea su propia red la primera vez (portal cautivo)** y guarda varias redes wifi | Cumple D2 y D4 |
| P5 | **Sin deshacer, ~8 colores y 3 grosores en la primera versión** | Primera versión mínima |
| P6 | **Empezar con una sola placa de 4,3" (~12 €)** antes de comprar las dos | Valida pantalla, táctil, lápiz y wifi con poco gasto |
| P7 | **Evitar tldraw y otras librerías con licencia comercial** | Lo exige D9 |
| P8 | **Relay alojado primero en casa (PC/Raspberry con túnel)** y, si hace falta, en un servidor de pago pequeño | Coste 0 al empezar |
| P9 | **Mismo modelo de placa en las dos casas** | Evita bandas negras y diferencias de lienzo |

## 6. Decisiones abiertas

- Tamaño final de pantalla: 4,3" o 7" (D11), que se resuelve tras el prototipo (P6).
- Dónde se aloja el relay (P8).
- Entorno de desarrollo del firmware (PlatformIO propuesto).
- Si compramos en AliExpress/eBay o en tienda UE (D14).
