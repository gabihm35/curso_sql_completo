# 05. Expresiones Comunes de Tabla (CTEs) y CTEs Recursivas

## 📖 Teoría

Una **CTE** (*Common Table Expression*) es un conjunto de resultados temporal con nombre que existe solo durante el ámbito de ejecución de una única sentencia `SELECT`, `INSERT`, `UPDATE` o `DELETE`.

### Ventajas sobre Subconsultas:
- **Legibilidad:** Permite descomponer consultas complejas en bloques modulares y fáciles de mantener.
- **Reutilización:** Se puede hacer referencia a una CTE múltiples veces dentro de la consulta principal.
- **Recursividad:** Habilita la navegación por estructuras jerárquicas o en árbol (organigramas, categorías anidadas, grafos).

---

## 💻 Ejemplos Prácticos

### A. CTE Simple (`WITH`)
Simplifica la legibilidad al separar la lógica de procesamiento antes del agrupamiento final:

```sql
WITH ventas_altas AS (
    SELECT 
        cliente_id, 
        SUM(monto_total) AS total_gastado
    FROM ordenes
    WHERE fecha >= '2026-01-01'
    GROUP BY cliente_id
    HAVING SUM(monto_total) > 10000.00
)
SELECT 
    c.nombre, 
    c.email, 
    va.total_gastado
FROM ventas_altas va
INNER JOIN clientes c ON c.cliente_id = va.cliente_id
ORDER BY va.total_gastado DESC;
```

### CTE Recursiva (With Recursive):

- **Miembro Ancla (Anchor Member):** La consulta base que genera el primer conjunto de filas sin llamar a la CTE.
- **Miembro Recursivo (Recursive Member):** Consulta que hace referencia al propio nombre de la CTE y define la condición de parada mediante un JOIN.
- **Operador de Unión:** Generalmente UNION o UNION ALL.

## 💻 Ejemplo: Organigrama Jerárquico de Empleados

```sql
WITH RECURSIVE jerarquia_empleados AS (
    -- 1. Miembro Ancla: Seleccionar al Gerente General (quien no tiene jefe / jefe_id IS NULL)
    SELECT 
        empleado_id, 
        nombre, 
        puesto, 
        jefe_id, 
        1 AS nivel
    FROM empleados
    WHERE jefe_id IS NULL

    UNION ALL

    -- 2. Miembro Recursivo: Unir a los subordinados directos del nivel anterior
    SELECT 
        e.empleado_id, 
        e.nombre, 
        e.puesto, 
        e.jefe_id, 
        h.nivel + 1 AS nivel
    FROM empleados e
    INNER JOIN jerarquia_empleados h ON e.jefe_id = h.empleado_id
)
SELECT 
    nivel, 
    nombre, 
    puesto, 
    jefe_id
FROM jerarquia_empleados
ORDER BY nivel, jefe_id;