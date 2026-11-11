# Modificación 10: Grid Snap Conmutable (1mm, 5mm, 10mm), Ruteo Ortogonal de Barras (BUS) con Rubberbanding, Undo/Redo (Ctrl+Z/Ctrl+Y), Cajetín Inferior Interactivo y Zona de Exclusión

## Objetivo
Implementar un sistema integral de ingeniería CAD eléctrica en el simulador que incorpore:
1. Selección y cambio rápido de paso de rejilla (*Grid Snap Size*: 1mm, 5mm, 10mm) mediante la tecla **`G`** con indicador en el footer.
2. Modo de trazado de barras y conductores eléctricos (**BUS**) con **enrutamiento ortogonal a 90°** (*Orthogonal Routing*) y **deformación elástica** (*Orthogonal Rubberbanding*) al desplazar componentes conectados.
3. Finalización de trazado con **clic izquierdo**, selección y resaltado con **clic derecho**, eliminación con tecla **`Supr` (Delete)** y edición de etiqueta/visibilidad del BUS mediante **doble clic derecho** con modal dedicado.
4. Historial de acciones **Deshacer (Undo, `Ctrl + Z`)** y **Rehacer (Redo, `Ctrl + Y`)** con botones e iconos dedicados junto a los controles de Zoom.
5. Cajetín inferior corrido (*Full-Width Bottom Title Strip*) dentro del plano, con celdas editables interactivamente mediante modal al hacer clic en cada celda.
6. **Bloqueo estricto del cajetín**: zona de exclusión física donde no se permite soltar componentes CAD ni trazar rutas de conductores.

---

## 1. Grid Snap Size Conmutable (1mm, 5mm, 10mm) con Tecla 'G'
* **Definición de Valores de Paso (Snap Pitch)**:
  * **1 mm**: Paso fino ($\approx 4\text{ px}$).
  * **5 mm**: Paso estándar de esquemas eléctricos ($\approx 20\text{ px}$).
  * **10 mm**: Paso amplio para alineación rápida ($\approx 40\text{ px}$).
* **Atajo de Teclado**:
  * Al pulsar la tecla **`G`** (o `Shift + G`), se cicla secuencialmente: `1mm` $\rightarrow$ `5mm` $\rightarrow$ `10mm` $\rightarrow$ `1mm`.
* **Indicador en el Status Bar (Footer)**:
  * Mostrar una píldora visual en el footer inferior:  
    `Grid Snap: 5mm (Press 'G')` con estado activo.
* **Comportamiento en Canvas**:
  * Todas las coordenadas de arrastre de componentes, puntos de soltado y vértices de cableado (*routing*) se alinean al múltiplo entero más cercano del `snapSize` activo:
    $$\text{posSnap} = \text{round}\left(\frac{\text{pos}}{\text{snapStep}}\right) \times \text{snapStep}$$

---

## 2. Activación de Ruteo de Barras / Conductores ("BUS")
* **Botón BUS en Barra de Herramientas**:
  * Al presionar el botón **BUS** (o pulsar la tecla rápida **`B`**):
    * Se activa el modo de trazado (`isBusRoutingActive = true`).
    * El cursor del lienzo cambia a mira de precisión técnica (`cursor-crosshair`).
    * El botón BUS se resalta con fondo azul activo e indicador luminoso pulsante.
    * Si se vuelve a presionar o se presiona `Escape`, se cancela el modo de trazado.

---

## 3. Ruteo Ortogonal (90°) y Deformación Elástica (Orthogonal Rubberbanding)
* **Trazado Ortogonal tipo Proteus con Clic Sostenido / Arrastre**:
  * Con la herramienta **BUS** activa:
    * **Presionar clic sostenido (`mousedown`)**: fija el punto de origen o terminal de salida.
    * **Arrastrar el ratón**: traza dinámicamente el conductor en segmentos ortogonales estrictos a $90^\circ$ (horizontal y vertical, sin diagonales).
    * **Soltar el ratón (`mouseup`)**: fija y finaliza la ruta en el workspace, registrándola en la estructura de datos del proyecto.
* **Deformación Elástica Ortogonal (*Orthogonal Rubberbanding*)**:
  * Si un componente conectado a un cable o bus se mueve en el workspace:
    * La conexión **no se desconecta**.
    * Los segmentos ortogonales intermedios se alargan, acortan o desplazan elásticamente manteniendo todos los ángulos a $90^\circ$.
    * Los bornes conectados conservan su integridad física.

---

## 4. Interacción con Rutas: Modo "SELECT", Menú Emergente, Eliminación y Edición
* **Comportamiento en Modo "SELECT" (Herramienta Puntero Normal)**:
  * **Selección con Clic Derecho**:
    * Al hacer **clic derecho** sobre una ruta existente, esta se resalta visualmente con un contorno brillante de selección y se define como `selectedRouteId`.
    * **Supresión del Menú del Navegador**: Se aplica `event.preventDefault()` al hacer clic derecho sobre elementos del lienzo para evitar que el menú contextual nativo de Windows/navegador aparezca sobre el plano.
  * **Eliminación con Tecla `Supr` (Delete)**:
    * Al pulsar la tecla **`Delete`** o **`Supr`**, si hay una ruta seleccionada, se elimina inmediatamente del lienzo y se registra en el historial de acciones.
  * **Doble Clic (Herramienta SELECT) - Modal de Propiedades del BUS**:
    * Al hacer **doble clic** sobre una ruta en modo `SELECT`, se abre directamente el modal: **"Propiedades de Barra / Conductor"** (`#modal-bus-properties`).
    * Al ejecutarse dentro del modo SELECT, no existe conflicto alguno con el menú contextual del navegador.
    * **Campo de Texto**: *Nombre / Etiqueta del BUS* (ej. `BUS 115 kV - SE Norte`, `Alimentador Radial 13.8kV`, `2000A`).
    * **Checkbox**: *Mostrar etiqueta en el plano* (`checked: true/false`).
    * **Botones**: "Guardar" y "Cancelar".
    * Si el checkbox está marcado, la etiqueta se renderiza flotante y alineada sobre el tramo principal del conductor en tipografía monospace técnica.

---

## 5. Historial Undo / Redo (`Ctrl + Z` y `Ctrl + Y`) con Botones en la Barra de Herramientas
* **Pilas de Historial**:
  * `historyStack = []`: almacena instantáneas de estado del canvas (componentes colocados, posiciones, rutas, propiedades).
  * `redoStack = []`: almacena estados deshechos listos para rehacer.
* **Acciones Registradas**:
  * Insertar / colocar componente.
  * Mover componente en el workspace.
  * Eliminar componente.
  * Trazar nueva ruta de BUS.
  * Eliminar ruta de BUS.
  * Modificar etiqueta o propiedades del BUS.
* **Atajos de Teclado**:
  * `Ctrl + Z`: Deshace la última acción (*Undo*).
  * `Ctrl + Y` (o `Ctrl + Shift + Z`): Rehace la última acción deshecha (*Redo*).
* **Botones en la Barra de Herramientas del Canvas**:
  * Ubicados a la izquierda/junto a los controles de Zoom (`Zoom Out`, `Zoom In`, `Fit`):
    * **Botón Deshacer**: Icono de flecha curvada izquierda (↺ / `Undo`), con tooltip `"Deshacer (Ctrl+Z)"`.
    * **Botón Rehacer**: Icono de flecha curvada derecha (↻ / `Redo`), con tooltip `"Rehacer (Ctrl+Y)"`.
  * Se atenúan con `opacity-40 cursor-not-allowed` cuando su pila respectiva esté vacía.

---

## 6. Cajetín Inferior Corrido (*Bottom Title Strip*) con Celdas Editables Interactivas
* **Estructura y Dimensiones**:
  * Integrado dentro del contenedor de hoja `#engineering-sheet-boundary` en la parte inferior (`bottom: 0`, `left: 0`, `width: 100%`, altura fija de $44\text{ px}$).
  * Bordes técnicos que combinan con el marco de la hoja.
* **Celdas Principales**:
  1. **Celda Empresa / Logotipo**: Logotipo y membrete `ICIS Power Systems`.
  2. **Celda Proyecto / Ubicación**: Nombre de la subestación y responsable técnico.
  3. **Celda Diagrama / Código**: Tipo de plano y código de identificación (ej. `PCD-SLD-115-001`).
  4. **Celda Control / Metadatos**: Revisión (`1.0`), Paginación (`Hoja 1/1`), Fecha actual y Formato activo (`ANSI B`).
* **Edición Interactiva por Celda**:
  * Al hacer **clic en cualquier celda del cajetín**:
    * Se despliega un modal emergente: **"Editar Propiedad de Cajetín"** prellenado con el valor de la celda pulsada.
    * Al guardar, el cajetín actualiza instantáneamente su texto en el plano y almacena los metadatos en el proyecto.

---

## 7. Bloqueo Estricto de la Zona del Cajetín (Zona de Exclusión)
* **Límite Físico de Soltado de Componentes**:
  * Se establece la cota superior del cajetín como límite inferior no traspasable:
    $$Y_{\max} = \text{sheetHeight} - \text{cajetinHeight} - \text{componentHeight} - 8\text{ px}$$
  * Ninguna pieza puede ser arrastrada, movida o soltada dentro del cajetín. Si se intenta soltar sobre el cajetín, su coordenada $Y$ se autoajusta automáticamente por encima del límite superior del cajetín.
* **Límite de Ruteo de Barras y Cables**:
  * Las rutas y conductores de BUS no pueden atravesar ni terminar dentro del cajetín. El cursor de ruteo restringe los puntos válidos a $Y \le \text{sheetHeight} - \text{cajetinHeight}$.

---

## Archivos Afectados
* `docs/screens/home_screen.html`
