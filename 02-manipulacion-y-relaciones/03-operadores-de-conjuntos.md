# 04. Operadores de Conjuntos (`UNION`, `INTERSECT`, `EXCEPT`)

## 📖 Teoría

Los **operadores de conjuntos** permiten combinar las filas resultantes de dos o más consultas `SELECT` independientes en un único conjunto de resultados. Operan bajo los principios de la teoría de conjuntos matemática.

### Reglas Estrictas para Operaciones de Conjuntos:
1. **Mismo número de columnas:** Cada consulta `SELECT` debe retornar exactamente la misma cantidad de columnas.
2. **Tipos de datos compatibles:** Las columnas en la misma posición relativa deben tener tipos de datos iguales o convertibles de forma implícita.
3. **Nombres de encabezados:** El resultado final adoptará los nombres de columna definidos en la **primera** consulta `SELECT`.
4. **Ordenamiento:** La cláusula `ORDER BY` solo puede declararse una vez, al final de todo el bloque del operador.

---

### Tipos de Operadores:

* **`UNION`:** Combina los resultados de dos consultas y **elimina las filas duplicadas**.
* **`UNION ALL`:** Combina los resultados de dos consultas **conservando los duplicados**. *(Es significativamente más rápido porque no requiere una fase interna de ordenamiento/deduplicación)*.
* **`INTERSECT`:** Devuelve únicamente las filas presentes en **ambas** consultas (intersección).
* **`EXCEPT`** (o `MINUS` en Oracle/MySQL antiguo): Devuelve las filas de la primera consulta que **no existen** en la segunda consulta (diferencia de conjuntos).

---

## 💻 Ejemplos Prácticos

### Configuración del Escenario
Imaginemos dos tablas de contactos: `clientes` y `proveedores`.

```sql
-- 1. UNION (Sin duplicados)
SELECT nombre, email 
FROM clientes
UNION
SELECT nombre, email 
FROM proveedores
ORDER BY nombre ASC;

-- 2. UNION ALL (Incluyendo duplicados)
SELECT nombre, email, 'Cliente' AS tipo_persona
FROM clientes
UNION ALL
SELECT nombre, email, 'Proveedor' AS tipo_persona
FROM proveedores;

-- 3. INTERSECT (Personas que son tanto clientes como proveedores)
SELECT email 
FROM clientes
INTERSECT
SELECT email 
FROM proveedores;

-- 4. EXCEPT (Clientes que NO son proveedores)
SELECT email 
FROM clientes
EXCEPT
SELECT email 
FROM proveedores;