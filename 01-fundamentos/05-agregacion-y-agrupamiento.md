# 05. Funciones de Agregación y Agrupamiento (`GROUP BY` / `HAVING`)

## 📖 Teoría

Las funciones de agregación operan sobre conjuntos de filas para retornar un único valor escalar descriptivo.

* **Funciones Básicas:**
  - `COUNT()`: Cuenta el número de filas o valores no nulos.
  - `SUM()`: Calcula el total de una columna numérica.
  - `AVG()`: Calcula el promedio numérico.
  - `MIN()` / `MAX()`: Devuelven el valor mínimo y máximo respectivamente.
* **`GROUP BY`:** Divide los datos en grupos lógicos según los valores de una o varias columnas para aplicar las funciones de agregación a cada grupo.
* **`HAVING`:** Filtra **grupos** creados por `GROUP BY` basándose en el resultado de funciones de agregación (el `WHERE` filtra filas individuales *antes* del agrupamiento).

### Orden Lógico de Ejecución en Consultas SQL:
1. `FROM`
2. `WHERE`
3. `GROUP BY`
4. `HAVING`
5. `SELECT`
6. `ORDER BY`

---

## 💻 Ejemplos Prácticos

```sql
-- 1. Agregación Global
SELECT 
    COUNT(*) AS total_productos,
    AVG(precio_unitario) AS precio_promedio,
    MIN(precio_unitario) AS precio_minimo,
    MAX(precio_unitario) AS precio_maximo
FROM productos;

-- 2. Agrupamiento con GROUP BY
SELECT 
    categoria_id, 
    COUNT(*) AS cantidad_productos,
    SUM(stock) AS stock_total
FROM productos
GROUP BY categoria_id;

-- 3. Filtrado de agrupaciones con HAVING
SELECT 
    categoria_id, 
    AVG(precio_unitario) AS precio_promedio
FROM productos
WHERE stock > 0
GROUP BY categoria_id
HAVING AVG(precio_unitario) > 50.00
ORDER BY precio_promedio DESC;