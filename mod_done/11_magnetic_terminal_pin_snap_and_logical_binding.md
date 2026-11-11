# Modificación 11: Sistema de Bornes Magnéticos (Pin Terminal Snapping), Detección de Proximidad y Vinculación Lógica de Rutas

## Objetivo
Implementar un sistema paramétrico de **bornes y pines de conexión magnéticos** (*Magnetic Terminal Pin Snapping*) en el lienzo CAD, de modo que al trazar rutas con la herramienta **BUS**:
1. El cursor detecte y se imante automáticamente al centro exacto de los pines de los equipos colocados en el workspace (interruptores, relés, transformadores de corriente y de potencia).
2. Se muestre un indicador visual de imantación (*Snap Indicator Ring* verde/azul con etiqueta de borne) al acercarse a menos de $14\text{ px}$ de cualquier terminal.
3. Las rutas guarden la **vinculación lógica** con los pines de origen y destino (`fromTerminal` y `toTerminal`).
4. El **Orthogonal Rubberbanding** se ancle de manera milimétrica a las coordenadas exactas de los pines físicos del equipo al mover cualquier pieza, conservando la conectividad eléctrica y los ángulos a $90^\circ$.

---

## 1. Definición y Coordenadas Relativas de Pines por Categoría de Equipo
Cada componente instanciado en el workspace (`placedCanvasElements`) tiene un conjunto de terminales definidos según su geometría vectorial de 130×90 px:

| Categoría | Identificador de Pines | Coordenadas Relativas `(relX, relY)` | Etiqueta Visible | Función Eléctrica |
| :--- | :--- | :--- | :--- | :--- |
| **Interruptor (ANSI 52)** | `pin_1` | `(65, 8)` | **1** | Entrada de potencia superior |
| | `pin_2` | `(65, 72)` | **2** | Salida de potencia inferior |
| **Relé (ANSI 50/51/87/21)** | `pin_1` | `(18, 28)` | **1** | Entrada de corriente / medición A |
| | `pin_2` | `(18, 48)` | **2** | Entrada de corriente / medición B |
| | `pin_3` | `(112, 28)` | **3** | Salida de disparo / contacto NO (Trip) |
| | `pin_4` | `(112, 48)` | **4** | Salida de disparo / común aux. |
| **Transformador de Corriente (CT)** | `pin_P1` | `(16, 45)` | **P1** | Barra primaria de entrada |
| | `pin_P2` | `(114, 45)` | **P2** | Barra primaria de salida |
| | `pin_S1` | `(57, 10)` | **S1** | Secundario de medición 1 |
| | `pin_S2` | `(73, 10)` | **S2** | Secundario de medición 2 |
| **Transformador de Tensión (VT)** | `pin_H1`, `pin_H2` | `(16, 30)`, `(16, 50)` | **H1, H2** | Primarios de alta tensión |
| | `pin_X1`, `pin_X2` | `(108, 30)`, `(108, 50)` | **X1, X2** | Secundarios de medición |
| **Transformador de Potencia (XFMR)** | `pin_H1`, `pin_H2` | `(18, 28)`, `(18, 52)` | **H1, H2** | Devanados primarios de potencia |
| | `pin_X1`, `pin_X2` | `(110, 28)`, `(110, 52)` | **X1, X2** | Devanados secundarios de potencia |

---

## 2. Detección de Proximidad e Imantación Magnética (*Magnetic Snap*)
* **Umbral de Atracción**:
  * Radio de captura magnética: $14\text{ px}$.
* **Comportamiento en Modo `BUS`**:
  * Durante el movimiento del ratón (`mousemove`), la aplicación calcula la distancia entre el cursor y las coordenadas absolutas de todos los terminales en la hoja:
    $$\text{dist} = \sqrt{(\text{cursorX} - (el.x + pin.relX))^2 + (\text{cursorY} - (el.y + pin.relY))^2}$$
  * **Si $\text{dist} \le 14\text{ px}$**:
    * El punto del cable se "imanta" directamente al centro exacto del borne:
      $$\text{routePoint} = (el.x + pin.relX, el.y + pin.relY)$$
    * Se dibuja en la capa SVG un **indicador de imantación**:
      * Anillo circular verde esmeralda o azul cian (`r = 7px`, borde grueso o pulsante).
      * Etiqueta emergente con el nombre del pin y equipo (ej. `SEL-751 : Pin 1`, `52_CB1 : Pin 2`).
  * **Si $\text{dist} > 14\text{ px}$**:
    * El cursor opera con el paso de cuadrícula regular (`snapCoord`).

---

## 3. Vinculación Lógica en la Estructura de la Ruta (`fromTerminal` y `toTerminal`)
* Al completar la ruta (`mouseup`), si el punto inicial o final estuvo imantado a un pin, se guarda la referencia:
  ```javascript
  {
    id: "ROUTE-...",
    x1, y1, x2, y2, cornerX, cornerY,
    label: "BUS 1",
    showLabel: true,
    fromTerminal: { instanceId: "INST-VCPW-01", pinId: "pin_2" } || null,
    toTerminal:   { instanceId: "INST-SEL751-01", pinId: "pin_1" } || null
  }
  ```
* Esto permite identificar formalmente qué equipo está conectado con cuál para futuras simulaciones de flujo de carga o coordinación de protecciones.

---

## 4. Orthogonal Rubberbanding Milimétrico al Pin
* Al mover un equipo en el workspace (`startMovingPlacedElement`):
  * La aplicación busca todas las rutas donde `fromTerminal.instanceId === element.instanceId` o `toTerminal.instanceId === element.instanceId`.
  * En tiempo real durante el arrastre:
    * Si la ruta está conectada en el inicio:
      $$\text{route.x1} = element.x + pin.relX$$
      $$\text{route.y1} = element.y + pin.relY$$
    * Si la ruta está conectada en el fin:
      $$\text{route.x2} = element.x + pin.relX$$
      $$\text{route.y2} = element.y + pin.relY$$
      $$\text{route.cornerX} = \text{route.x2}$$
  * El cable permanece ruteado ortogonalmente a $90^\circ$ y anclado con exactitud absoluta al borne físico del componente.

---

## 5. Resaltado Visual de Bornes Conectados
* Los pines que tienen al menos una ruta conectada se renderizan con un punto central de unión/soldadura eléctrico (`fill="#2563eb"` o nodo eléctrico sólido de $3.5\text{ px}$) para indicar visualmente que el borne está enlazado a un circuito activo.

---

## 6. Compatibilidad con Undo/Redo y Zona de Exclusión
* Los cambios en las conexiones y vinculaciones a pines se almacenan en el historial `historyStack` para permitir `Ctrl + Z` y `Ctrl + Y`.
* Ningún pin dentro de la zona de exclusión del cajetín inferior puede recibir conexiones.

---

## Archivos Afectados
* `docs/screens/home_screen.html`
