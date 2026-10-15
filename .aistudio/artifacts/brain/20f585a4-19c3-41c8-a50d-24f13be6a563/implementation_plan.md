# Plan Avanzado de Decoración Exterior Hiperrealista para Circuito 2 (Speedway GP)

Plan de arquitectura gráfica e ingeniería de rendimiento para transformar el entorno perimetral del Circuito 2 en un resort alpino italiano de alta gama con lago orgánico especular y villas VIP contemporáneas visibles desde el cockpit a 350+ km/h, manteniendo una tasa de refresco ultra fluida de 60–120 FPS sin alterar la iluminación ni el atardecer existentes.

---

### User Review & Critical Decisions

> [!IMPORTANT]
> **Decisiones Confirmadas en Fase 1:**
> - **Estética Perimetral:** Complejo vacacional y villas VIP de arquitectura italiana alpina / Riviera lacustre contemporánea (líneas aerodinámicas, voladizos suaves, terrazas panorámicas con vidrio y bronce champagne).
> - **Distribución Visual en Pista:** Elementos distribuidos estratégicamente en tres zonas focales de alta visibilidad: colina del Curvone (Turn 2), ladera exterior de la horquilla (Hairpin Turn 4) y la mega recta de meta (1.1 km a 355+ km/h).
> - **Cuerpo de Agua:** Lago alpino orgánico con agua PBR de alta especularidad, oleaje sutil, costa de guijarros y orilla natural integrada con el terreno.
> - **Condición Lumínica:** La iluminación ambiental, el ángulo solar del atardecer y la paleta crepuscular actual se mantienen 100% intactos.

---

### 1. Overview & Core Concept

- **Qué entrega esta mejora:** Convierte los exteriores vacíos y desérticos del Circuito 2 (2.780 m) en un paisaje hiperrealista de la región de los lagos del norte de Italia (Lago di Como / Garda). Mientras el piloto compite a más de 350 km/h, contempla en el horizonte villas VIP suspendidas en voladizo, reflejos especulares de agua alpina, vegetación mediterránea esculpida y terrazas de observación integradas en la topografía.
- **Público Objetivo:** Jugadores y pilotos virtuales que buscan inmersión gráfica AAA con fidelidad de simulador profesional sin sufrir tirones, micro-stutters ni caídas de frametime.
- **Valor Diferencial:** Toda la riqueza geométrica (formas curvas suaves, curvatura orgánica, barandillas de vidrio, sombras arrojadas y reflejos Fresnel) se añade con **cero impacto en fluidez**, gracias a técnicas de GPU Instancing, *batching* estático por cuadrantes espaciales y mallas no indexadas pre-fusionadas.

---

### 2. User Experience & Visual Design

#### A. Zonas Focales y Recorrido Visual desde la Pista
1. **Foco 1: La Mega Recta de Salida y Meta (X: -100 a +350, Z: -180 a -120)**
   - **Línea de visión:** El piloto encara la recta a fondo (300–355 km/h).
   - **Decoración exterior:** En el flanco exterior izquierdo, por encima del muro de protección, se extiende el Paseo Lacustre y la Villa Presidencial del Club Náutico: arquitectura horizontal con aleros aerodinámicos, fachada de cristal reflectante, dársena para lanchas Riva clásicas y mástiles con banderas iluminadas.
2. **Foco 2: La Colina y Curvone de Alta Velocidad (X: +400 a +520, Z: -50 a +180)**
   - **Línea de visión:** Curva peraltada de radio amplio a 280 km/h con vista panorámica abierta hacia el este.
   - **Decoración exterior:** Lago Alpino Esmeralda (`Alpine Lake Basin`) con silueta orgánica que abraza el exterior de la curva. En la orilla opuesta del lago, un resort aterrazado con techos ondulados biónicos que reflejan los rayos rasantes del atardecer.
3. **Foco 3: La Horquilla y Curva Lenta (X: -60 a +120, Z: +280 a +380)**
   - **Línea de visión:** Punto de frenada fuerte (de 330 km/h a 85 km/h), donde el piloto tiene 3 a 4 segundos de visión directa hacia la ladera exterior.
   - **Decoración exterior:** Conjunto de 3 Villas VIP contemporáneas escalonadas en terrazas de piedra natural y estuco toscano suave, jardines colgantes con cipreses italianos esbeltos, barandillas de vidrio curvo y piscinas de borde infinito que desbordan hacia el lago.

#### B. Lenguaje Visual, Materiales y Acabados (Hiperrealismo sin Dibujo Pobre)
- **Prohibición de geometrías primitivas:** Nada de cajas rectangulares planas o conos rígidos. Todos los edificios y estructuras utilizarán aristas biseladas (*chamfered bevels*), voladizos elípticos, columnas cilíndricas cónicas y faldones aerodinámicos modelados con splines Catmull-Rom continuos.
- **Materiales PBR Calibrados:**
  - *Agua Alpina PBR:* Shader PBR con `roughness: 0.08`, `metalness: 0.15`, color base azul petróleo profundo (`#0f2b38`) y cálculo de reflectividad Fresnel para reflejar el cielo y la luz rasante del atardecer con brillos nítidos.
  - *Mármol y Travertino Pulido:* Acabado mineral mate-suave (`#e2d9cc`, `roughness: 0.45`) para las terrazas y zócalos de las villas.
  - *Bronce Champagne y Titanio Cepillado:* Perfiles metálicos de ventanas y pilares (`#c5a880`, `metalness: 0.82`, `roughness: 0.28`).
  - *Vidrio Tintado Estructural:* Superficie translúcida de alta especularidad (`#1e293b`, `roughness: 0.12`, `opacity: 0.85`).
  - *Piedra Esculpida y Orilla de Guijarros:* Transición suave entre la tierra orgánica y el agua del lago mediante franjas de ribera modeladas.

---

### 3. Key Product Decisions & Trade-Offs

- **Decisión 1: Malla de Agua PBR en un Solo Draw Call vs. Simulación Dinámica por CPU**
  - *Enfoque Elegido:* Malla de lago modelada con geometría no indexada orgánica y material `MeshStandardMaterial` con mapa de normales de agua y deformación en vértice ligera.
  - *Por qué:* Garantiza 0 overhead de CPU por frame, permitiendo que la GPU renderice el agua y sus brillos especulares con un solo comando de dibujo.
  - *Alternativas descartadas:* Shaders complejos de refracción en pantalla (Screen-Space Refraction) que consumen demasiado ancho de banda de memoria y reducen los FPS en portátiles y móviles.

- **Decisión 2: Fusión de Villas por Material (*Material-Bucket Merging*)**
  - *Enfoque Elegido:* Todas las villas y pabellones de la ladera se generan acumulando sus geometrías en 4 listas (`travertineGeos`, `bronzeMetalGeos`, `glassGeos`, `woodDeckGeos`) y se fusionan con `safeMergeBufferGeometries`.
  - *Por qué:* Permite construir 6 villas de arquitectura compleja y rica en detalles con **únicamente 4 Draw Calls** en total para todo el conjunto arquitectónico.
  - *Alternativas descartadas:* Importar múltiples modelos GLB pesados sin optimizar que añadirían cientos de llamadas de renderizado y pausas de recolección de basura (*Garbage Collection*).

- **Decisión 3: Vegetación Mediterránea (Cipreses y Olivos) en InstancedMesh con Culling Espacial**
  - *Enfoque Elegido:* Reutilizar el motor `OrganicVegetationSystem` integrando configuraciones de cipreses altos (*Italian Cypress*) y masas de pinos mediterráneos con orientación natural hacia la pista, agrupados en cuadrantes espaciales.
  - *Por qué:* El jugador percibe bosques densos y laderas vivas mientras el motor solo ejecuta 2–3 llamadas de instanciado para cientos de árboles.

---

### 4. Technical Architecture & Data Strategy

```
┌────────────────────────────────────────────────────────────────────────┐
│                   SPEEDWAY WORLD GENERATION PIPELINE                   │
│                       (SpeedwayTrackBuilder.ts)                        │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
          ┌─────────────────────────┼─────────────────────────┐
          │                         │                         │
          ▼                         ▼                         ▼
┌──────────────────┐      ┌──────────────────┐      ┌──────────────────┐
│  Track Ribbon &  │      │ Paddock & Garages│      │  Outfield Resort │
│  Curbs / Runoffs │      │ & Pit Architecture│     │  & Alpine Basin  │
└──────────────────┘      └──────────────────┘      └────────┬─────────┘
                                                             │
                                   ┌─────────────────────────┴─────────────────────────┐
                                   ▼                                                   ▼
                    ┌──────────────────────────────┐                    ┌──────────────────────────────┐
                    │     Alpine Lake & Shore      │                    │    Italian Riviera Villas    │
                    │   (AlpineLakeBasinBuilder)   │                    │ (AlpineResortArchitect.ts)   │
                    ├──────────────────────────────┤                    ├──────────────────────────────┤
                    │ • Organic spline shoreline   │                    │ • Curved cantilever terraces │
                    │ • Specular PBR water mesh    │                    │ • Bronze champagne trim      │
                    │ • Pebble & stone rim bevel   │                    │ • Structural tinted glass    │
                    │ • Pre-merged (1 Draw Call)   │                    │ • Batched (4 Draw Calls)     │
                    └──────────────────────────────┘                    └──────────────────────────────┘
                                   │                                                   │
                                   └─────────────────────────┬─────────────────────────┘
                                                             ▼
                                            ┌─────────────────────────────────┐
                                            │   GPU HARDWARE INSTANCING &     │
                                            │      STATIC SCENE FREEZE        │
                                            ├─────────────────────────────────┤
                                            │ • Cipreses y pinos perimetrales │
                                            │ • Soft baked shadows            │
                                            │ • MatrixAutoUpdate = false      │
                                            │ • Presupuesto total: ≤ 8 calls  │
                                            └─────────────────────────────────┘
```

#### Presupuesto de Rendimiento para el Circuito 2:
- **Draw Calls Adicionales Máximos:** $\le 8$ llamadas de dibujo para toda la nueva decoración exterior.
- **VRAM Adicional:** $< 12 \text{ MB}$ (geometrías unificadas y texturas reutilizadas).
- **CPU Frametime:** $0.00 \text{ ms}$ en runtime loop (todas las matrices estáticas quedan congeladas en `matrixAutoUpdate = false`).
- **Tasa de Cuadros:** 60 FPS estables en GPU integrada; 120 FPS en tarjetas dedicadas.
