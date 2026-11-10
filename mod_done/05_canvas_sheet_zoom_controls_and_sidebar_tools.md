# Modificación: Controles de Canvas, Formato de Hoja, Zoom con Rueda, Navegación de Pestañas y Reorganización de Herramientas CAD

## Objetivo
Optimizar la interacción del lienzo CAD en `docs/screens/home_screen.html`: expandir el espacio de trabajo eliminando el marco anidado, implementar zoom mediante rueda de ratón y controles dedicados, incorporar selector de formato de hoja técnica (ANSI / ISO), reubicar herramientas de dibujo CAD en el sidebar izquierdo y ajustar la visibilidad contextual según la pestaña activa.

---

## 1. Visibilidad Contextual en Sidebar (Ocultar en Pestaña Home)
* Los controles ubicados en la sección **"Device List"** (botones *add*/*del*, el *TextBox* de dispositivo seleccionado y las opciones *Select/Bus/Net*):
  * **No deben mostrarse** cuando el usuario se encuentre en la pestaña inicial fija **"Workspace / Home"** (o subviews generales como *Home Overview*, *Recent Projects* o *Templates*).
  * Deben **mostrarse automáticamente** únicamente cuando el usuario tenga abierta y activa una pestaña de proyecto / diagrama CAD.

---

## 2. Icono de Ventana de Nuevo Proyecto
* En las nuevas ventanas/pestañas de proyecto creadas a través del modal:
  * El icono representativo debe ser idéntico al de **"Single Line Diagram"** (`⚡`, color ámbar/dorado).

---

## 3. Desplazamiento Activo con Flechas de Scroll de Pestañas
* Al presionar las flechas de desplazamiento de pestañas (**`‹`** y **`›`**):
  * El usuario no solo debe desplazar visualmente la tira de pestañas, sino **cambiar y activar la pestaña de proyecto anterior o siguiente** correspondiente.

---

## 4. Reubicación de Herramientas CAD (Select, Bus, Net) al Sidebar
* Debajo del **TextBox** de "Device List" en el sidebar izquierdo:
  * Incorporar un grupo de herramientas CAD en serie con las opciones:
    1. **Select** (Herramienta de selección / puntero)
    2. **Bus** (Inserción de barra conductora)
    3. **Net** (Conexión / línea conductora)
  * Mantener indicador de herramienta activa al hacer clic.

---

## 5. Lienzo Directo (Eliminación de "Interactive Diagram Canvas Frame")
* Eliminar el contenedor interno `<!-- Interactive Diagram Canvas Frame -->` (`max-w-4xl max-h-[460px]`).
* Utilizar directamente el contenedor principal **`<!-- CAD Graphic Diagram Canvas -->`** como área total de trabajo y dibujo para los elementos y dispositivos seleccionados desde el TextBox.

---

## 6. Zoom Interactivo con Rueda del Ratón
* Al situar el cursor sobre el contenedor **`<!-- CAD Graphic Diagram Canvas -->`**:
  * Permitir hacer **Zoom In** y **Zoom Out** suavemente utilizando la rueda del ratón (`wheel` event).
  * Limitar la escala mínima y máxima (ej. 25% a 400%).

---

## 7. Recuadro de Formato de Hoja de Ingeniería (ANSI / ISO)
* Dentro del área de trabajo del canvas:
  * Dibujar un **recuadro azul con borde técnico** que represente las dimensiones proporcionales del formato de hoja de ingeniería seleccionado (Formatos ANSI o Formato A).
  * Centrar el recuadro dentro del espacio del lienzo.

---

## 8. Selector de Formato de Hoja en Barra Superior de Controles
* En el contenedor `<!-- Canvas Interactive Controls -->` (donde anteriormente se encontraban los botones Select, Bus y Net):
  * Colocar un menú desplegable (**dropdown list `<select>`**) con los formatos de hoja disponibles:
    * **ANSI A** (Letter 8.5" × 11")
    * **ANSI B** (Tabloid 11" × 17")
    * **ANSI C** (17" × 22")
    * **ANSI D** (22" × 34")
    * **ISO A4** (210 × 297 mm)
    * **ISO A3** (297 × 420 mm)
    * **ISO A2** (420 × 594 mm)
    * **ISO A1** (594 × 841 mm)
  * Al cambiar el formato en el dropdown, actualizar dinámicamente las dimensiones del recuadro azul en el canvas.

---

## 9. Controles de Zoom y Botón "Fit In"
* Al lado izquierdo del selector de formato de hoja:
  * **Botón "Fit In"**: botón con icono de centrado/ajuste que reestablece el zoom al 100% y recentra la hoja en la vista.
  * **Botón Zoom Out (`−`)**: disminuye el nivel de zoom en pasos de 10% / 25%.
  * **Etiqueta de Zoom**: texto que indica el valor porcentual actual (ej. `100%`).
  * **Botón Zoom In (`+`)**: incrementa el nivel de zoom.

---

## 10. Bloqueo de Botón "Run Study"
* Mantener el botón **"Run Study"** completamente deshabilitado/bloqueado:
  * Aplicar atributo `disabled`.
  * Utilizar opacidad reducida (`opacity-40` o `opacity-50`) y filtro de escala de grises (`grayscale cursor-not-allowed`) para denotar su estado inactivo de fondo.

---

## Archivos Afectados
* `docs/screens/home_screen.html`
