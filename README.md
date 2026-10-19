<h1 align="center">Cuadro e-ink</h1>

<p align="center">
  Pantalla e-ink a color sobre el escritorio que muestra lo que le manda un servidor propio.<br>
  Duerme y consulta cada pocos minutos; funciona con batería.
</p>

<p align="center">
  <img alt="Estado: diseño" src="https://img.shields.io/badge/estado-dise%C3%B1o-orange">
  <img alt="Licencia: MIT" src="https://img.shields.io/badge/licencia-MIT-blue">
  <img alt="Hardware: ESP32-S3" src="https://img.shields.io/badge/hardware-ESP32--S3-red">
</p>

---

## Qué es

Un cuadro de 7,3" a color (e-ink Spectra 6, seis tintas) sin táctil, pensado para una persona poco técnica: se enciende, se conecta al wifi y muestra lo que el servidor le envía. Quien está al otro extremo, con más nivel técnico, hace las cosas desde el servidor: imágenes, widgets o programas.

## Hardware

Placa Seeed XIAO ePaper Display Board EE04 (ESP32-S3) y panel Seeed de 7,3" Spectra 6, 800×480; batería LiPo de una celda y caja impresa en 3D.

## Estado

Fase de diseño. Todavía no hay código; el software irá después.

## Documentación

- [Decisiones de diseño](docs/decisiones-de-diseno.md): estrategia, qué queremos y por qué.
- [Documentación técnica](docs/documentacion-tecnica.md): cómo se materializan esas decisiones.
- [Software existente](docs/software-existente.md): firmware abierto que se podría reutilizar.

## Licencia

[MIT](LICENSE)
