---
layout: page
title: Boost Sincrónico 75W (12V to 36V) con Control en Doble Lazo
description: Modelado en espacio de estados, control PI multirrata en ESP32 y validación experimental para acople de bus DC.
img: assets/pcb3d.jpg
importance: 1
category: Electrónica de Potencia y Control
related_publications: false
---

## 01 — Descripción General
Trabajo integrador de **Electrónica de Potencia (EPW103-1, ITM)** enfocado en el diseño, auditoría de hardware, modelado matemático, simulación y validación experimental de un **convertidor DC-DC Boost sincrónico de 75 W** sobre la PCB estandarizada `Boost_Converter_sch v4` / `Boost_Converter_bdr v3`.

El control en cascada digital (lazo interno de corriente y lazo externo de tensión) mitiga la fase no mínima asociada al cero en el semiplano derecho (**RHPZ**, $873.1\text{ Hz}$ con $L_0 = 350\ \mu\text{H}$ sin polarización DC) y amortigua la resonancia del filtro LC.

## 02 — Etapas del Sistema

* **Etapa de Potencia Sincrónica:** Dos MOSFETs canal N IRFZ44N ($Q_1$ principal y $Q_2$ rectificador sincrónico) a $30\ \text{kHz}$ en CCM, inductor toroidal Micrometals T157-52 (Mix-52, $L_0 \approx 350\ \mu\text{H}$ sin sesgo DC y $L_{\text{eff}} = 213.6\ \mu\text{H}$ estimado a $6.25\ \text{A}$, rizado de $1.25\ \text{A}$ / $20\%$ e $I_{L,\min} = 5.625\ \text{A} > 0$) y capacitor de diseño $C_o = 220\ \mu\text{F}$.
* **Acondicionamiento y Gate Driver:** Reguladores 7815TV y 7805TV, adaptación con 2N7000, driver IR2110 a $15\ \text{V}$ con etapa *bootstrap* (1N4148 y $0.47\ \mu\text{F}$) y enclavamiento por LM555N al pin `SD`.
* **Sensado:** Sensor ACS712ELCTR-30A ($66\ \text{mV/A}$, calibración inicial de *offset* con 500 muestras) para corriente del inductor y divisor resistivo ($120\ \text{k}\Omega / 10\ \text{k}\Omega$, $K_{Vo} = 13$) para tensión de salida.
* **Control Digital (ESP32):** Lazos PI discretos por Euler hacia adelante con *anti-windup*, rampa de referencia y protecciones; lazo interno a $T_s = 150\ \mu\text{s}$ ($6.67\ \text{kHz}$, $K_{p,i} = 0.020$, $K_{i,i} = 10.5\ \text{s}^{-1}$) y lazo externo multirrata cada $600\ \mu\text{s}$ ($K_{p,v} = 0.276$, $K_{i,v} = 72.5\ \text{s}^{-1}$), con control en el Núcleo 1 y comunicación serie en el Núcleo 0.

## 03 — Ficha Técnica y Resultados

| Parámetro / Resultado | Valor / Observación |
| :--- | :--- |
| Conversión nominal | $12\ \text{V}$, $36\ \text{V}$, $75\ \text{W}$ de diseño |
| Frecuencia de conmutación | $f_{\text{sw}} = 30\ \text{kHz}$ (PWM LEDC, 11 bits) |
| Periodo de control multirrata | $T_s = 150\ \mu\text{s}$ (corriente) · $T_v = 600\ \mu\text{s}$ (tensión) |
| Respuesta en modelo MATLAB | Establecimiento $\sim 17\ \text{ms}$ y sobreimpulso $\sim 0.6\%$ ($C_o = 220\ \mu\text{F}$, $T_d = 150\ \mu\text{s}$, $T_v = 600\ \mu\text{s}$) |

## 04 — Seguridad y Buenas Prácticas
* Verificar polaridad y orientaciones antes de energizar, limitar tensión y corriente en pruebas de banco y cuidar las etapas de sensado, disparo y aislamiento.


## Diseño PCB y Validación Experimental

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    <img src="{{ '/assets/pcb2d.jpg' | relative_url }}" alt="Layout 2D de la PCB" class="img-fluid rounded z-depth-1" />
  </div>
  <div class="col-sm mt-3 mt-md-0">
    <img src="{{ '/assets/pcb3d.jpg' | relative_url }}" alt="Modelo 3D de la PCB" class="img-fluid rounded z-depth-1" />
  </div>
</div>
<div class="caption">
  Izquierda: Layout 2D del convertidor DC-DC Boost sincrónico de 75 W. Derecha: Modelo 3D de la PCB.
</div>

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    <img src="{{ '/assets/montajeboost.jpeg' | relative_url }}" alt="Montaje físico en banco de pruebas" class="img-fluid rounded z-depth-1" />
  </div>
  <div class="col-sm mt-3 mt-md-0">
    <img src="{{ '/assets/visualizacionenpantalla.jpeg' | relative_url }}" alt="Visualización en pantalla y telemetría" class="img-fluid rounded z-depth-1" />
  </div>
</div>
<div class="caption">
  Izquierda: Montaje físico en banco de pruebas. Derecha: Visualización en pantalla y telemetría.
</div>

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    <img src="{{ '/assets/simulacion.jpg' | relative_url }}" alt="Simulación del convertidor DC-DC Boost de 75 W" class="img-fluid rounded z-depth-1" />
  </div>
  <div class="col-sm mt-3 mt-md-0">
    <img src="{{ '/assets/imagen1.jpeg' | relative_url }}" alt="Pruebas experimentales del prototipo en laboratorio" class="img-fluid rounded z-depth-1" />
  </div>
</div>
<div class="caption">
  Izquierda: Simulación del convertidor. Derecha: Evidencia experimental en laboratorio.
</div>
