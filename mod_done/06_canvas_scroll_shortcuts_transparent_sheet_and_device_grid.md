# Modificación: Desplazamiento de Canvas (Scroll Vertical/Horizontal), Hoja Transparente con Grid y List View de Dispositivos en Sidebar

## Objetivo
Optimizar la navegación y dibujo en el lienzo CAD en `docs/screens/home_screen.html`: implementar atajos de rueda de ratón para desplazamiento vertical y horizontal de barras de scroll, convertir el marco de hoja de ingeniería en un contorno azul transparente que refleje el fondo de cuadrícula (`grid`), y reemplazar el TextBox de dispositivos en el sidebar izquierdo por un ListView en cuadrícula de 2 columnas con previsualización de símbolos CAD y tooltips informativos.

---

## 1. Atajos de Rueda de Ratón para Desplazamiento (Scroll Bars)
* **Desplazamiento Vertical**:
  * Al presionar la combinación de tecla **`Ctrl` + Rueda del ratón** (o rueda estándar según configuración del viewport), desplazar suavemente la barra de desplazamiento vertical (`scrollTop`) del contenedor `<!-- CAD Graphic Diagram Canvas -->`.
* **Desplazamiento Horizontal**:
  * Al presionar **`Shift` + Rueda del ratón** (o **`Ctrl + Shift` + Rueda del ratón**), desplazar suavemente la barra de desplazamiento horizontal (`scrollLeft`) del contenedor `<!-- CAD Graphic Diagram Canvas -->`.
* **Mantenimiento de Zoom**:
  * Reservar el zoom para rueda directa o botones dedicados de la barra de controles (`Zoom In`, `Zoom Out`, `Fit In`), permitiendo al usuario navegar fluidamente con barras de desplazamiento bidireccionales.

---

## 2. Hoja de Trabajo Transparente (Fondo Grid Continuo)
* En el contenedor `<!-- Zoomable Sheet Workspace Transform Wrapper -->` y el recuadro de hoja `#engineering-sheet-boundary`:
  * **Eliminar el color de fondo blanco sólido**: aplicar fondo transparente (`bg-transparent`), permitiendo que la cuadrícula técnica de fondo (`bg-grid-pattern`) del viewport sea visible en toda la superficie.
  * **Mantener únicamente el borde azul técnico**: conservar el contorno perimetral azul (`border-2 border-blue-600`) que delimita el formato de hoja seleccionado (ANSI / ISO).
  * **Interior despejado**: dejar el área interior completamente limpia y vacía, lista para la colocación directa de símbolos y componentes eléctricos por el usuario.

---

## 3. Reemplazo del TextBox por ListView de Dispositivos (2 Columnas con Símbolos CAD)
* En la sección **"Device List"** del sidebar izquierdo:
  * **Eliminar** el campo de texto simple (`TextBox / input text`).
  * **Incorporar** un contenedor tipo **List View en cuadrícula de 2 columnas**:
    * Mostrar **2 piezas en serie por fila** (`grid grid-cols-2 gap-2`).
    * Cada pieza debe estar encerrada en un **recuadro individual** (`border border-slate-300 rounded-lg bg-white p-2 text-center hover:border-blue-500 hover:shadow-sm cursor-pointer transition-all`).
    * **Símbolo CAD**: cada recuadro debe mostrar el símbolo esquemático CAD vectorial de la pieza seleccionada (relé 50/51 o 87, interruptor 52, transformador CT/VT o de potencia).
    * **Tooltip informativo**: al pasar el cursor sobre cada recuadro, desplegar un tooltip flotante con la información técnica completa del componente (Nombre, Fabricante, Código ANSI, Rating y Parámetros).
    * **Dimensiones**: contenedor con altura máxima delimitada (`max-h-48` o `max-h-56`) y barra de desplazamiento vertical activa (`overflow-y-auto no-scrollbar` o scroll fino).
* **Integración con Botones "add" y "del"**:
  * Al presionar el botón azul **"add"** y confirmar en el modal de librería, agregar la pieza seleccionada al List View de 2 columnas.
  * Al hacer clic en una pieza del List View, marcarla como seleccionada.
  * Al presionar el botón rojo **"del"**, eliminar la pieza seleccionada del List View.

---

## Archivos Afectados
* `docs/screens/home_screen.html`
