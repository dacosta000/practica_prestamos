# Práctica: Préstamo de equipo en Odoo

- **Nombre:** Darwin Fernando Acosta García
- **Número de cuenta:** 0801200108285

## Respuestas

### Paso 1. Catálogo de equipos
1. **¿Qué pasa con un equipo cuando se desmarca el campo Activo? ¿Por qué es mejor que borrarlo?**
   El registro se archiva (deja de aparecer en las búsquedas y listas normales), pero sigue existiendo en la base de datos. Es mejor que borrarlo porque conserva la integridad referencial y el historial de los préstamos antiguos en los que participó ese equipo.
2. **¿Para qué sirve `copy=False` en el campo `codigo`?**
   Evita que al duplicar un registro de equipo desde la interfaz de Odoo se copie el mismo código de inventario, obligando a asignar un código nuevo y evitando duplicados.

### Paso 2. Registro de préstamos
1. **Este modelo usa `_name` y `_inherit` al mismo tiempo. ¿Qué hace cada uno aquí?**
   `_name = "prestamo.prestamo"` crea un modelo nuevo con su propia tabla en PostgreSQL, mientras que `_inherit = ["mail.thread"]` incorpora (mezcla) los campos y métodos de mensajería y seguimiento de cambios (chatter) dentro de ese modelo nuevo.
2. **¿Qué hace `tracking=True` en el campo `state`?**
   Registra automáticamente en el chatter del formulario cada cambio de valor que sufra el campo `state` (quién lo cambió, cuándo y cuál era el valor anterior y el nuevo).

### Paso 3. Equipos del préstamo
1. **En `fields.One2many("prestamo.linea", "prestamo_id")`, ¿qué indica cada parámetro?**
   El primer parámetro (`"prestamo.linea"`) indica el modelo hijo donde están las líneas, y el segundo (`"prestamo_id"`) indica el campo `Many2one` en ese modelo hijo que apunta de regreso al préstamo padre.
2. **¿Qué pasa con las líneas cuando se borra un préstamo? ¿Qué parte del código lo define?**
   Se eliminan automáticamente en cascada junto con el préstamo. Lo define el parámetro `ondelete="cascade"` en el campo `prestamo_id` del modelo `prestamo.linea`.

### Paso 4. Flujo de estados
1. **Los botones ya se ocultan según el estado. ¿Por qué los métodos vuelven a validar el estado?**
   Porque ocultar un botón en la vista XML solo restringe la interfaz visual, pero un usuario o proceso podría invocar el método directamente mediante RPC/API o desde una pestaña desactualizada. Validar en Python garantiza la integridad de la lógica de negocio en el servidor.
2. **¿Qué hace `prestamo.linea_ids.equipo_id.write(...)`? ¿Por qué no hace falta un `for` para cada equipo?**
   Toma el recordset con todos los equipos asociados a las líneas del préstamo y actualiza su campo `estado` en una sola operación SQL masiva. En el ORM de Odoo, los recordsets permiten navegar relaciones y ejecutar `write()` sobre múltiples registros a la vez sin iterar manualmente.

### Paso 5. Reglas del préstamo
1. **El campo `equipo_id` ya filtra los equipos disponibles. ¿Por qué hace falta el TODO 5?**
   Porque mientras un préstamo permanece en estado `borrador`, otro usuario puede prestar ese mismo equipo en otro préstamo o enviarlo a mantenimiento. Al pulsar `Prestar`, el sistema debe volver a comprobar en tiempo real que el equipo siga disponible.
2. **¿En qué momento se ejecuta un método con `@api.constrains`? ¿Y la validación de `action_prestar`?**
   `@api.constrains` se ejecuta automáticamente cada vez que se crea o guarda (`create` / `write`) un registro en la base de datos. En cambio, la validación dentro de `action_prestar` se ejecuta únicamente cuando el usuario presiona el botón Prestar.

### Paso 6. Contacto, kanban y búsqueda
1. **¿Qué pasa si arrastra una tarjeta del kanban a otra columna? ¿Por qué?**
   La tarjeta se mueve de columna y el campo `estado` del equipo se actualiza automáticamente al valor de esa columna, porque la vista kanban está agrupada por `estado` (`default_group_by="estado"`).
2. **El campo `prestamo_ids` se agregó a `res.partner`. ¿Se crea una columna nueva en la tabla de contactos? Explique.**
   No se crea ninguna columna nueva en la tabla `res_partner`. Los campos `One2many` son virtuales en el ORM y consultan dinámicamente los registros de `prestamo_prestamo` filtrando por la clave foránea `partner_id` que ya existe en la tabla de préstamos.