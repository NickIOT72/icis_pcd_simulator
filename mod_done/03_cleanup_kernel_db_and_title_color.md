# Modificación: Limpieza de Estado de Kernel, Base de Datos y Estilos de Título

## Objetivo
Refinar la interfaz de usuario en `docs/screens/home_screen.html` eliminando referencias a la sincronización de base de datos y al kernel de cálculo que no deben mostrarse, y ajustar el color del título principal en la barra superior.

## Cambios Requeridos
1. **Sidebar izquierdo**:
   - Eliminar el recuadro "Calculation Kernel" (contenedor con estado Online, NodeJS + Python 3.11 y PostgreSQL Sync Active).
2. **Footer (Segmento 7)**:
   - Eliminar las etiquetas de estado "DB" (`DB: PostgreSQL (Connected)`) y "Kernel" (`Kernel: NodeJS / Python 3.11`).
   - Mantener el resto de los elementos del footer (Ready, Workspace, Active Project, Auto-save).
3. **Barra Superior (Top Header)**:
   - Cambiar el texto "Protection & Control Designer" a color negro sólido en su totalidad (reemplazando cualquier fragmento coloreado en azul).
4. **Modal "New Project"**:
   - Eliminar el indicador/recuadro inferior izquierdo de "PostgreSQL Sync" (`PostgreSQL Sync: protection_db (Active)`).

## Archivos Afectados
- `docs/screens/home_screen.html`
