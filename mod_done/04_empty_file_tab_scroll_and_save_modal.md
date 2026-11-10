# Modificación: Opción "Empty file", Limpieza de Formulario, Modal de Guardado al Cerrar, Scroll de Pestañas, Reorganización de Sidebar y Modal Device Library

## Objetivo
Actualizar el modal de creación de proyectos, la gestión de pestañas/ventanas, la estructura del sidebar izquierdo en `docs/screens/home_screen.html`, y el diseño e integración interactiva del modal `modal_device_library.html`.

---

## 1. Cambio de Etiqueta en Modal
* En el selector de plantillas iniciales (Sección 3 del modal):
  * Cambiar el texto **"Blank Canvas"** por **"Empty file"**.
  * Mantener la descripción y el valor del radio button adaptados a este término.

---

## 2. Generación de Ventana Vacía y Reseteo del Formulario
* Al hacer clic en **"Create Project"**:
  * Si la plantilla seleccionada es **"Empty file"**:
    * Abrir una nueva pestaña/ventana con un lienzo de diseño (CAD canvas) que contenga únicamente el fondo con cuadrícula (`grid`), sin componentes ni diagramas pre-dibujados.
  * **Limpieza de formulario**:
    * Limpiar todas las casillas escritas por el usuario (`modal-project-name`, `modal-engineer`, `modal-description`).
    * Dejar el botón **"Create Project"** nuevamente bloqueado/deshabilitado para la siguiente apertura.

---

## 3. Confirmación de Cierre y Modal de Guardado
* Al hacer clic en el botón de cierre (**`×`**) de una pestaña de proyecto (especialmente si no se ha guardado o es un proyecto nuevo):
  * **Interrumpir el cierre directo** y abrir un diálogo modal de confirmación con la pregunta:
    > *"¿Desea guardar los cambios en [Nombre_del_Proyecto.pcd] antes de cerrar?"*
  * Opciones de acción:
    1. **Guardar (Save):** Ejecuta la rutina de guardado del archivo, notifica la acción y cierra la pestaña.
    2. **No Guardar (Don't Save):** Descarta los cambios pendientes y cierra la pestaña inmediatamente.
    3. **Cancelar (Cancel):** Cierra el diálogo de confirmación y mantiene la pestaña abierta sin cambios.

---

## 4. Flechas de Desplazamiento de Pestañas ("Tab Scroll Arrows")
* En la barra de pestañas superior de Segmento 5 (`browser-tab-bar`):
  * Si se abren múltiples pestañas y estas exceden el ancho visible del contenedor:
    * Incorporar botones con flechas de desplazamiento (**`<`** y **`>`**) en los extremos de la barra de pestañas.
    * Permitir desplazar horizontalmente la tira de pestañas de forma suave al hacer clic en las flechas.

---

## 5. Reorganización del Sidebar Izquierdo (Tools y Device List)
* **Renombrar sección**:
  * Cambiar el título **"Designer Toolkits"** por **"Tools"**.
* **Separar "Device List"**:
  * Quitar **"Device List (CT/PT)"** de la lista de herramientas de "Tools".
  * Crear una nueva sección debajo de "Tools" con el mismo formato de encabezado: **"Device List"**.
* **Controles de "Device List"**:
  * Debajo del encabezado "Device List", colocar una fila con 2 botones alineados en serie:
    * Botón azul: **"add"**
    * Botón rojo: **"del"**
  * Debajo de la fila de botones, colocar un **text box** (campo de entrada/búsqueda de dispositivos).

---

## 6. Modal Device Library e Integración con Home Screen
* **Diseño en Lista (Left Segment)**:
  * En el contenedor *"Left: Device Selection Grid (Scrollable)"*, cambiar la presentación de tarjetas (`cards`) por un formato de **filas de lista (list items)**, manteniendo la misma información técnica (icono, nombre, fabricante, código ANSI/categoría, especificaciones eléctricas y estado de calibración).
  * Insertar **múltiples elementos adicionales** (relés SEL, ABB, Siemens, Schneider; CTs/VTs GE, ABB; interruptores; transformadores) para garantizar y comprobar que el contenedor tenga barra de desplazamiento vertical activa (`overflow-y-auto`).
* **Selección Interactiva**:
  * Al hacer clic en un elemento de la lista:
    * Marcarlo visualmente como **seleccionado** (borde y fondo azul de selección activa).
    * Deseleccionar automáticamente el resto de los elementos.
    * Actualizar dinámicamente los datos, atributos de ingeniería y el símbolo esquemático en el contenedor derecho (*"Right: Detailed Specifications Preview Panel"*).
* **Filtro de Categorías por Dropdown**:
  * En el contenedor *"Category Filter Pills"*, reemplazar los botones/píldoras por un **menú desplegable (dropdown list `<select>`)** que filtre la lista de dispositivos según su categoría (Relays, Transformers, Breakers, Switchgear, etc.).
* **Integración con Home Screen**:
  * Al hacer clic en el botón azul **"add"** de la sección "Device List" en el sidebar izquierdo de `home_screen.html`, abrir este modal de selección de dispositivos.
  * Al presionar **"Select Device"** en el modal, transferir automáticamente el nombre del dispositivo seleccionado al **text box** ubicado debajo de "Device List" en `home_screen.html`, y cerrar el modal.

---

## Archivos Afectados
* `docs/screens/home_screen.html`
* `docs/screens/modal_device_library.html`
