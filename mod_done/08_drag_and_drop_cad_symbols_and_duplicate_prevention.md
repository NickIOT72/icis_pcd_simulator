# Modificación: Prevención de Duplicados en Device List, Bloqueo en Modal y Arrastre (Drag & Drop) de Símbolos CAD al Workspace

## Objetivo
Implementar control de unicidad de componentes para evitar elementos repetidos en el sidebar, bloquear la selección en el modal si la pieza ya existe, y dotar al lienzo CAD de funcionalidad interactiva de arrastre y soltado (*Drag & Drop*) con previsualización translúcida, numeración de terminales y movilidad posterior en el workspace.

---

## 1. Prevención de Elementos Duplicados y Bloqueo en Modal
* **Unicidad en Sidebar**:
  * El List View del sidebar izquierdo no debe admitir componentes repetidos (mismo identificador de catálogo, ej. `DEV-SEL-751`).
* **Validación Dinámica en Modal "Device Library"**:
  * Al seleccionar cualquier componente en la lista del modal:
    * Comprobar si su identificador (`id`) ya está presente en la colección `sidebarDevices`.
    * **Si ya existe en la lista**:
      * Deshabilitar el botón **"Select Device"** (`disabled = true`).
      * Aplicar estilo de bloqueo visual (`opacity-50`, `grayscale cursor-not-allowed`, fondo atenuado).
      * Cambiar el texto del botón o mostrar etiqueta de advertencia: *"Already in Device List"* / *"Ya añadido"*.
    * **Si no existe**:
      * Habilitar normalmente el botón **"Select Device"** con su estilo azul/verde activo y texto estándar.

---

## 2. Arrastre (Drag) con Previsualización Translúcida y Terminales Numeradas
* **Inicio del Arrastre (`dragstart` / `mousedown`)**:
  * Al hacer clic sostenido en una tarjeta del List View del sidebar y desplazar el cursor hacia el área del canvas:
    * Iniciar el modo de arrastre de componente.
    * Desplegar un elemento fantasma o previsualización flotante (*drag preview*) con opacidad reducida (`opacity-60` o `opacity-70`).
* **Detalle del Símbolo CAD y Terminales Numeradas**:
  * La previsualización debe mostrar el **símbolo eléctrico CAD vectorial** correspondiente al dispositivo.
  * Debe incluir **puntos de conexión (bornes/terminales) numerados** de acuerdo con la naturaleza del equipo:
    * **Interruptores (ANSI 52)**: Terminales **1** (superior/entrada) y **2** (inferior/salida).
    * **Transformadores de Corriente (CT)**: Terminales de línea **P1**, **P2** y secundarios **S1**, **S2** (o **1**, **2**, **3**, **4**).
    * **Relés de Protección (ANSI 50/51/87)**: Bornes de disparo y medición numerados (**1**, **2**, **3**, **4**).
    * **Transformadores de Potencia**: Bornes primarios (**H1**, **H2**) y secundarios (**X1**, **X2**).

---

## 3. Soltado (Drop) y Fijación en el Workspace
* **Acción de Soltado (`drop` / `mouseup`)**:
  * Al liberar el cursor sobre el lienzo CAD (dentro del contenedor de hoja `#engineering-sheet-boundary` o el viewport):
    * Fijar de forma permanente una instancia del símbolo CAD en las coordenadas exactas `(X, Y)` del punto de soltado (descontando el desplazamiento de scroll y factor de zoom).
    * Asignar un identificador único de instancia a la pieza colocada (ej. `INST-SEL751-01`).
    * El símbolo debe mostrarse nítido (`opacity-100`) con sus etiquetas de nombre y terminales numeradas visibles.

---

## 4. Movilidad Posterior de Componentes en el Workspace
* **Reposicionamiento Libre**:
  * Cualquier pieza fijada en el workspace debe ser **movible**:
    * Al hacer clic sostenido sobre la pieza colocada, permitir arrastrarla a cualquier otra coordenada dentro del lienzo CAD.
    * Al soltar el mouse, actualizar su posición final.
  * Mantener indicador de selección activa (resaltado perimetral azul claro) al interactuar con el componente en el lienzo.

---

## Archivos Afectados
* `docs/screens/home_screen.html`
