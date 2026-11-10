# Modificación: Visualización de Múltiples Naturalezas/Toolkits por Proyecto

## Objetivo
Actualizar la visualización de los proyectos recientes en `docs/screens/home_screen.html` (tanto en la vista general como en la tabla de Recent Projects) para soportar múltiples naturalezas/toolkits por proyecto.

## Reglas de visualización
1. **Icono principal**: Mostrar el icono correspondiente a la naturaleza o toolkit de la **última modificación**.
2. **Badge adicional (+N)**: Si el proyecto tiene más de 1 tipo de naturaleza, mostrar un indicador con el formato `+N` (donde `N` es la cantidad de naturalezas restantes).
3. **Tooltip interactivo**: Al pasar el mouse (`hover`) sobre el número `+N`, desplegar un tooltip que liste los nombres de todos los toolkits/naturalezas asociados al proyecto.

## Archivos afectados
- `docs/screens/home_screen.html`
