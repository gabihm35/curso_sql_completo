# 01. Combinación de Tablas (`INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL JOIN`, `CROSS JOIN`)

## 📖 Teoría

Los **JOINs** permiten combinar filas de dos o más tablas basándose en una columna en común (clave foránea / clave primaria) para construir un único conjunto de resultados.

### Tipos de JOINs:

1. **`INNER JOIN`:** Devuelve únicamente las filas donde existe una coincidencia exacta entre ambas tablas. Si una fila de la tabla A no coincide con ninguna de la tabla B, queda excluida.
2. **`LEFT JOIN` (o `LEFT OUTER JOIN`):** Devuelve **todas** las filas de la tabla de la izquierda (A), y las coincidencias de la tabla de la derecha (B). Si no hay coincidencia, las columnas de la tabla derecha tendrán valores `NULL`.
3. **`RIGHT JOIN` (o `RIGHT OUTER JOIN`):** Devuelve **todas** las filas de la tabla de la derecha (B), y las coincidencias de la tabla de la izquierda (A). Si no hay coincidencia, las columnas de la tabla izquierda tendrán valores `NULL`.
4. **`FULL JOIN` (o `FULL OUTER JOIN`):** Devuelve todos los registros cuando hay una coincidencia en la izquierda o en la derecha. Si no hay coincidencia, rellena con `NULL` en el lado correspondiente.
5. **`CROSS JOIN`:** Produce un producto cartesiano (combina cada fila de la primera tabla con todas y cada una de las filas de la segunda). No utiliza la cláusula `ON`.
6. **`SELF JOIN`:** Un `JOIN` regular en el que una tabla se une consigo misma utilizando alias para comparar filas dentro de la misma estructura.

---

## 💻 Ejemplos Prácticos

### Tablas de Ejemplo:
- `clientes` (`cliente_id`, `nombre`)
- `pedidos` (`pedido_id`, `cliente_id`, `monto`, `fecha`)

```sql
-- 1. INNER JOIN: Obtener solo los pedidos de clientes registrados
SELECT 
    c.cliente_id,
    c.nombre,
    p.pedido_id,
    p.monto
FROM clientes c
INNER JOIN pedidos p ON c.cliente_id = p.cliente_id;

-- 2. LEFT JOIN: Listar TODOS los clientes, tengan o no pedidos realizados
SELECT 
    c.nombre,
    p.pedido_id,
    COALESCE(p.monto, 0.00) AS monto
FROM clientes c
LEFT JOIN pedidos p ON c.cliente_id = p.cliente_id;

-- 3. Identificar clientes SIN pedidos (Anti-JOIN patrón)
SELECT 
    c.cliente_id,
    c.nombre
FROM clientes c
LEFT JOIN pedidos p ON c.cliente_id = p.cliente_id
WHERE p.pedido_id IS NULL;

-- 4. FULL JOIN: Obtener todos los clientes y todos los pedidos (coincidan o no)
SELECT 
    c.nombre AS cliente,
    p.pedido_id
FROM clientes c
FULL JOIN pedidos p ON c.cliente_id = p.cliente_id;

-- 5. SELF JOIN: Jerarquía de empleados y sus respectivos supervisores
SELECT 
    e.nombre AS empleado,
    m.nombre AS supervisor
FROM empleados e
LEFT JOIN empleados m ON e.supervisor_id = m.empleado_id;