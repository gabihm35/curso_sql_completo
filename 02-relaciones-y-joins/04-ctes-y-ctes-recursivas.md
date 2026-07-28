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