# 03. Técnicas de Optimización de Consultas (*Query Tuning*)

## 📖 Teoría

El **Query Tuning** es el conjunto de prácticas y refactorizaciones aplicadas a las sentencias SQL para reducir la latencia, el uso de CPU, I/O de disco y memoria RAM en el servidor.

---

### Reglas de Oro para la Optimización:

1. **Evitar `SELECT *`:** Solicitar solo los campos necesarios reduce el I/O de red y habilita búsquedas de tipo `Index Only Scan`.
2. **Evitar aplicar funciones sobre columnas filtradas en el `WHERE`:**
   - ❌ *Incorrecto:* `WHERE YEAR(fecha_registro) = 2026` (Invalida el uso de índices B-Tree estándar - SARGable issue).
   - ✅ *Correcto:* `WHERE fecha_registro >= '2026-01-01' AND fecha_registro < '2027-01-01'`.
3. **Cuidado con `LIKE '%texto%'`:** Los comodines al inicio de la cadena impiden usar índices B-Tree. Utilizar `LIKE 'texto%'` o índices `GIN` con `pg_trgm`.
4. **Reemplazar Subconsultas Correlacionadas por `JOINs` o CTEs:** Las subconsultas en la cláusula `SELECT` se ejecutan fila por fila, causando un problema de complejidad $O(N)$.
5. **Utilizar `EXISTS` en lugar de `IN` para subconsultas:** `EXISTS` se interrumpe inmediatamente al encontrar la primera coincidencia (evaluación perezosa / short-circuit).
6. **Mantener Estadísticas Actualizadas:** Ejecutar periódicamente `ANALYZE` para que el optimizador tome decisiones informadas sobre la cardinalidad de los datos.

---

## 💻 Ejemplos Prácticos de Refactorización

### A. Corrección de columna envuelta en función (SARGable)

```sql
-- ❌ INEFICIENTE: Forzado a Seq Scan porque aplica LOWER() a cada fila de la tabla
SELECT * FROM clientes WHERE LOWER(apellido) = 'gomez';

-- ✅ EFICIENTE Opción 1: Guardar datos en minúsculas y buscar directamente
SELECT cliente_id, nombre, apellido FROM clientes WHERE apellido = 'Gomez';

-- ✅ EFICIENTE Opción 2: Usar un índice expresivo sobre LOWER(apellido)
CREATE INDEX idx_clientes_lower_apellido ON clientes(LOWER(apellido));
```

### B. Optimización de Subconsultas: IN vs EXISTS

```sql
-- ❌ INEFICIENTE: Carga toda la lista de la subconsulta en memoria antes de evaluar
SELECT * FROM clientes 
WHERE cliente_id IN (SELECT cliente_id FROM ordenes WHERE monto_total > 10000);

-- ✅ EFICIENTE: Evalúa la existencia y corta al primer match
SELECT c.cliente_id, c.nombre 
FROM clientes c
WHERE EXISTS (
    SELECT 1 
    FROM ordenes o 
    WHERE o.cliente_id = c.cliente_id 
      AND o.monto_total > 10000
);
```