# 02. Subconsultas (Correlacionadas y No Correlacionadas)

## 📖 Teoría

Una **subconsulta** (*subquery* o consulta anidada) es una instrucción `SELECT` ubicada dentro de otra instrucción SQL (como `SELECT`, `INSERT`, `UPDATE`, `DELETE` o dentro de la cláusula `FROM`/`WHERE`).

### Clasificación de Subconsultas:

1. **Subconsultas No Correlacionadas:**
   - Se ejecutan **una sola vez** antes de que la consulta principal sea procesada.
   - Son independientes de la consulta externa.
   - Pueden devolver un único valor escalar, una columna/lista o una tabla completa.
   - Operadores comunes: `=`, `>`, `<`, `IN`, `NOT IN`, `ANY`, `ALL`.

2. **Subconsultas Correlacionadas:**
   - Dependen de valores suministrados por la consulta externa.
   - Se ejecutan **una vez por cada fila** procesada por la consulta principal.
   - Suelen combinarse con los operadores `EXISTS` o `NOT EXISTS` para evaluar presencia/ausencia sin necesidad de contar o traer datos completos.

---

## 💻 Ejemplos Prácticos

```sql
-- 1. Subconsulta No Correlacionada Escalar
-- Obtener productos cuyo precio sea mayor al precio promedio general
SELECT nombre, precio_unitario
FROM productos
WHERE precio_unitario > (
    SELECT AVG(precio_unitario) 
    FROM productos
);

-- 2. Subconsulta No Correlacionada con operador IN
-- Obtener los clientes que han realizado pedidos de más de 500 USD
SELECT nombre, email
FROM clientes
WHERE cliente_id IN (
    SELECT DISTINCT cliente_id
    FROM pedidos
    WHERE monto > 500.00
);

-- 3. Subconsulta Correlacionada con EXISTS
-- Seleccionar categorías que tengan al menos un producto activo asociado
SELECT c.categoria_id, c.nombre
FROM categorias c
WHERE EXISTS (
    SELECT 1 
    FROM productos p 
    WHERE p.categoria_id = c.categoria_id 
      AND p.stock > 0
);

-- 4. Subconsulta Correlacionada en el SELECT
-- Mostrar cada producto junto con el precio promedio de su propia categoría
SELECT 
    p.nombre,
    p.precio_unitario,
    p.categoria_id,
    (
        SELECT AVG(p2.precio_unitario)
        FROM productos p2
        WHERE p2.categoria_id = p.categoria_id
    ) AS promedio_categoria
FROM productos p;