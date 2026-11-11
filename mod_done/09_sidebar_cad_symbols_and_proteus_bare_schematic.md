# Modificación: Símbolos CAD Vectoriales en Cards del Sidebar y Lógica Proteus (Símbolo Desnudo sin Caja) en el Workspace

## Objetivo
Reemplazar los iconos genéricos del panel lateral (*List View*) por los símbolos esquemáticos CAD vectoriales correspondientes, y aplicar la **lógica de diseño de Proteus** en el lienzo de trabajo (*workspace*), de modo que los componentes colocados se rendericen como símbolos eléctricos puros sin cajas, tarjetas ni fondos blancos perimetrales, con bornes y pines numerados proyectados directamente sobre la cuadrícula.

---

## 1. Tarjetas del Sidebar con Símbolo CAD Integrado (`sidebar-device-grid`)
* **Contenido Visual de la Tarjeta**:
  * Sustituir cualquier icono genérico o emoji por el **símbolo eléctrico CAD vectorial** de alta definición centrado en la tarjeta.
  * Mantener la proporción compacta (rejilla de 2 columnas).
  * Debajo del símbolo:
    * Nombre abreviado del modelo (ej. `Eaton VCP-W`, `SEL-751`, `ARTECHE CT`).
    * Etiqueta o código ANSI (ej. `ANSI 52`, `ANSI 50/51`, `CT 1200:5A`).
* **Interacción**:
  * Al pasar el ratón (*hover*): resaltado azul suave y despliegue del tooltip global con los datos nominales de ingeniería.
  * Al hacer clic sostenido (*dragstart*): el símbolo CAD se levanta flotando como previsualización translúcida (`opacity-70`).

---

## 2. Lógica Proteus en el Workspace (Símbolo Desnudo sin Caja Envolvente)
* **Eliminación de la Tarjeta/Caja Perimetral**:
  * En `#placed-cad-elements-container`, los componentes colocados en el lienzo **no deben tener caja contenedora blanca, ni borde de tarjeta perimetral, ni fondo opaco**.
  * El símbolo debe integrarse de forma pura y transparente directamente sobre la cuadrícula de la hoja de ingeniería (*engineering grid*).
* **Geometría Esquemática y Bornes Numerados**:
  * Cada símbolo debe mostrar sus líneas esquemáticas directas con sus terminales de conexión y bornes numerados:
    * **Interruptores (ANSI 52)**:
      * Línea de borne superior con terminal circular y etiqueta `1`.
      * Cuadro interruptor con cruz `X` de contacto.
      * Línea de borne inferior con terminal circular y etiqueta `2`.
      * Texto de referencia esquemática: Designador `52_CB1` y corriente nominal `1200A`.
    * **Transformadores de Corriente (CT)**:
      * Línea de barra primaria pasante con bornes `P1` y `P2`.
      * Toroide circular de medición `CT`.
      * Pines secundarios superiores con bornes `S1` y `S2`.
      * Texto de referencia: `CT1 (1200:5A)`.
    * **Transformadores de Tensión (VT)**:
      * Devanados acoplados con bornes primarios `H1`, `H2` y secundarios `X1`, `X2`.
    * **Transformadores de Potencia (XFMR)**:
      * Círculos entrelazados con bornes de alta tensión `H1`, `H2` y baja tensión `X1`, `X2`.
      * Texto de referencia: `TR1 115/13.8kV`.
    * **Relés de Protección (ANSI 50/51/87/21)**:
      * Círculo central con código de función ANSI (ej. `50/51`, `87B`).
      * 4 pines salientes con bornes numerados: `1` y `2` (medición/entrada izquierda), `3` y `4` (disparo/salida derecha).
      * Texto de referencia: `RLY1 (SEL-751)`.

---

## 3. Estado de Selección e Indicador Visual en el Workspace
* **Indicador de Selección Activa**:
  * Al hacer clic o seleccionar un componente en el lienzo:
    * No añadir recuadros blancos ni tarjetas opacas.
    * Mostrar un contorno perimetral fino discontinuo o guía azul (`outline outline-1 outline-blue-500 outline-offset-4` o puntos guía de esquina) que delimite la pieza seleccionada de manera técnica y limpia.
  * Al hacer clic en un área vacía de la hoja, se deselecciona y desaparece el contorno.

---

## 4. Movilidad Continua
* Las piezas permanecen plenamente movibles (*drag-to-move*) por todo el lienzo respetando el factor de zoom y los límites de la hoja de ingeniería.

---

## Archivos Afectados
* `docs/screens/home_screen.html`
