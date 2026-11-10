# Modificación: Pestañas Tipo Navegador en Center Workspace View

## Objetivo
Transformar el contenedor superior de **Center Workspace View (Segmento 5)** en una barra de navegación por pestañas estilo navegador web (*browser-like tab bar*), asegurando una pestaña raíz estática y fija.

## Requisitos de Diseño e Interacción
1. **Pestaña Estática / Fija (Pinned Tab)**:
   - Nombre por defecto: `Workspace / Inicio` (o el nombre de la subvista activa: *Home*, *Recent Projects*, *Templates*).
   - Estilizada con un icono representativo o de chincheta/home.
   - **No debe poder cerrarse** (sin botón `×`).
   - Muestra el viewport con las 3 subvistas intercambiables desde el menú lateral (`Home Overview`, `Recent Projects`, `Template Library`).

2. **Soporte de Pestañas Dinámicas**:
   - Pestañas adicionales que se abren a la derecha al hacer clic en "Open" en cualquier proyecto `.pcd` o plantilla.
   - Cada pestaña dinámica incluye:
     - Nombre del archivo/proyecto.
     - Icono de estado/naturaleza.
     - Botón de cierre (`×`).
   - Interacción de selección de pestaña activa cambiando el contenido del viewport.

3. **Integración con Navegación Lateral**:
   - Al seleccionar opciones del menú lateral (*Home Overview*, *Recent Projects*, *Template Library*), el foco regresa a la pestaña fija y actualiza su subvista correspondiente.

## Archivos Afectados
- `docs/screens/home_screen.html`
