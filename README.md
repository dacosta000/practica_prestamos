# Práctica: Préstamo de equipo en Odoo

- **Nombre:** Darwin Fernando Acosta García
- **Número de cuenta:** 0801200108285

## Respuestas
### Paso 1. Catálogo de equipos
1. **¿Qué pasa con un equipo cuando se desmarca el campo Activo? ¿Por qué es mejor que borrarlo?**
   El registro se archiva (deja de aparecer en las búsquedas y listas normales), pero sigue existiendo en la base de datos. Es mejor que borrarlo porque conserva la integridad referencial y el historial de los préstamos antiguos en los que participó ese equipo.
2. **¿Para qué sirve `copy=False` en el campo `codigo`?**
   Evita que al duplicar un registro de equipo desde la interfaz de Odoo se copie el mismo código de inventario, obligando a asignar un código nuevo y evitando duplicados.