# 04. Filtrado y Ordenamiento de Datos

## 📖 Teoría

El filtrado y ordenamiento te permiten manipular qué registros devolver y en qué secuencia presentarlos.

* **`WHERE`:** Evalúa condiciones lógicas fila por fila para incluir o excluir datos.
* **Operadores Lógicos y de Comparación:** `=`, `<>`, `>`, `<`, `>=`, `<=`, `AND`, `OR`, `NOT`.
* **`BETWEEN`:** Filtra en un rango inclusivo de valores.
* **`IN`:** Evalúa si el valor coincide con cualquier elemento de una lista fija.
* **`LIKE` / `ILIKE`:** Búsqueda de patrones en texto.
  - `%` representa cero o más caracteres.
  - `_` representa exactamente un caracter.
* **`IS NULL` / `IS NOT NULL`:** Comprueba la presencia o ausencia de datos.
* **`ORDER BY`:** Ordena el resultado por una o más columnas en sentido ascendente (`ASC`) o descendente (`DESC`).

---

## 💻 Ejemplos Prácticos

```sql
-- 1. Filtrado lógico combinado
SELECT nombre, precio_unitario, stock 
FROM productos
WHERE (categoria_id = 1 OR categoria_id = 2)
  AND stock > 0;

-- 2. Uso de BETWEEN y LIKE
SELECT * 
FROM productos
WHERE precio_unitario BETWEEN 50.00 AND 300.00
  AND nombre LIKE 'M%'; -- Nombres que comiencen con "M"

-- 3. Filtros con listas e IS NOT NULL
SELECT * 
FROM productos
WHERE categoria_id IN (1, 3, 5)
  AND codigo_sku IS NOT NULL;

-- 4. Ordenamiento multicolumna
SELECT nombre, precio_unitario, stock 
FROM productos
ORDER BY precio_unitario DESC, nombre ASC;