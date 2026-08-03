# 02. Análisis de Planes de Ejecución (`EXPLAIN` / `EXPLAIN ANALYZE`)

## 📖 Teoría

El **Optimizador de Consultas** (*Planner/Optimizer*) es el componente del motor encargado de analizar una consulta SQL y determinar la estrategia más eficiente para recuperar los datos.

Para inspeccionar cómo el optimizador planea y ejecuta una consulta, utilizamos los comandos:

* **`EXPLAIN`:** Muestra el **plan estimado** por el optimizador basándose en estadísticas internas (no ejecuta la consulta).
* **`EXPLAIN ANALYZE`:** **Ejecuta la consulta realmente** en la base de datos y muestra tanto el plan estimado como los **tiempos reales** de ejecución, número de filas procesadas y uso de memoria.

---

### Métrica y Nodos Clave en un Plan de Ejecución:

1. **Costo (`cost=start_cost..total_cost`):** Unidad de medida arbitraria del esfuerzo estimado por el motor.
2. **Métodos de Acceso a Tablas:**
   - **`Seq Scan` (Sequential Scan):** Lee la tabla completa desde el disco de principio a fin. Lento en tablas grandes.
   - **`Index Scan`:** Usa un índice para encontrar punteros a filas en la tabla y luego lee esas filas.
   - **`Index Only Scan`:** Los datos solicitados en el `SELECT` están **completamente dentro del índice**. No necesita leer la tabla base (*cobertura total*).
   - **`Bitmap Index Scan / Heap Scan`:** Lee múltiples punteros del índice, crea un mapa de bits en memoria y lee las páginas de la tabla en orden de disco para optimizar el I/O.
3. **Algoritmos de Join:**
   - **`Nested Loop`:** Compara cada fila de la primera tabla con todas las filas de la segunda (bueno para conjuntos pequeños).
   - **`Hash Join`:** Crea una tabla Hash en memoria de la tabla más pequeña y escanea la otra.
   - **`Merge Join`:** Une dos conjuntos que ya están previamente ordenados.

---

## 💻 Ejemplos Prácticos

```sql
-- 1. Analizar la estimación sin ejecutar
EXPLAIN 
SELECT * FROM productos WHERE precio_unitario > 500.00;

-- 2. Analizar ejecución real con tiempos detallados y búferes de memoria
EXPLAIN (ANALYZE, BUFFERS, VERBOSE)
SELECT 
    c.nombre, 
    COUNT(o.orden_id) AS total_compras
FROM clientes c
INNER JOIN ordenes o ON c.cliente_id = o.cliente_id
WHERE o.fecha_orden >= '2026-01-01'
GROUP BY c.cliente_id, c.nombre;

/*
EJEMPLO DE LECTURA DE SALIDA (PostgreSQL):

HashAggregate  (cost=125.00..127.50 rows=200 width=40) (actual time=1.230..1.245 rows=150 loops=1)
  Group Key: c.cliente_id, c.nombre
  ->  Hash Join  (cost=35.50..115.00 rows=1000 width=36) (actual time=0.450..0.980 rows=950 loops=1)
        Hash Cond: (o.cliente_id = c.cliente_id)
        ->  Bitmap Heap Scan on ordenes o  ...
*/