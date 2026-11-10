# Modificación: Estado Inicial Vacío de Device List, Scrollbar Vertical Visible, Inserción desde Modal y Corrección de Tooltips Encapsulados

## Objetivo
Corregir y optimizar la interacción del contenedor **List View** de dispositivos en el sidebar izquierdo en `docs/screens/home_screen.html`: inicializar la lista en estado vacío, garantizar una barra de desplazamiento vertical visible, asegurar la inserción continua de componentes seleccionados desde el modal de librería, y desacoplar los tooltips técnicos para evitar que sean recortados/encapsulados por el `overflow` del contenedor con scroll.

---

## 1. Estado Inicial Vacío de Device List
* El List View ubicado debajo de los botones **"add"** y **"del"**:
  * Debe **iniciar completamente vacío** (`sidebarDevices = []`), sin componentes pre-cargados por defecto.
  * Cuando la lista esté vacía, mostrar un mensaje de estado limpio y centrado:
    > *"No hay dispositivos agregados. Presione 'add' para insertar piezas desde la librería."*

---

## 2. Barra de Desplazamiento Vertical Visible
* El contenedor del List View:
  * Debe contar con una altura máxima definida (`max-h-52` o `max-h-56`).
  * Debe incorporar una **barra de desplazamiento vertical visible** (`overflow-y-auto` con estilos explícitos de barra de desplazamiento: canal/track visible y corredera/thumb con buen contraste), evitando que quede oculta o recortada.

---

## 3. Inserción Dinámica desde el Modal "Device List"
* Cada vez que el usuario presione el botón azul **"add"**, explore la librería y haga clic en **"Select Device"**:
  * La pieza seleccionada debe incorporarse de forma inmediata al List View del sidebar.
  * El nuevo elemento agregado debe quedar automáticamente seleccionado (borde azul activo).
  * Si se agregan múltiples elementos consecutivos, la lista debe ir creciendo y activar el scroll vertical cuando sobrepase la altura máxima.

---

## 4. Corrección de Tooltips Encapsulados (Desacoplamiento de `overflow`)
* **Diagnóstico del problema**:
  * Al tener el contenedor List View la propiedad `overflow-y-auto` o `overflow: hidden`, los elementos con posición absoluta (`absolute`) que sobresalen de su marco sufren recorte (*clipping*), volviéndose invisibles o cortados.
* **Solución técnica**:
  * **Tooltip Flotante Global / Desacoplado**:
    * Implementar un elemento tooltip único a nivel de página o sidebar (`#device-grid-floating-tooltip`) fuera del contenedor con scroll.
    * Mediante eventos `mouseenter` y `mouseleave` (o `mousemove`), calcular las coordenadas exactas de la pieza en pantalla (`getBoundingClientRect()`) y posicionar el tooltip de manera flotante sin restricciones de recorte.
    * Alternativamente, desplegar el tooltip hacia la derecha del sidebar (`left: 100%`, `fixed` o anclado al sidebar exterior con `z-50`) mostrando:
      * Código ANSI / Categoría
      * Nombre completo del dispositivo
      * Fabricante
      * Rating / Especificaciones técnicas
      * Estado de calibración

---

## Archivos Afectados
* `docs/screens/home_screen.html`
