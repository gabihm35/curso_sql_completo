# 04. Particionamiento de Tablas (*Table Partitioning*)

## 📖 Teoría

El **Particionamiento** consiste en dividir una tabla conceptualmente grande en fragmentos físicos más pequeños y manejables llamados **particiones**.

A diferencia del sharding (que distribuye datos entre múltiples servidores), el particionamiento nativo ocurre **dentro de la misma instancia de base de datos**, manteniéndose invisible para las aplicaciones cliente (las consultas ejecutan contra la tabla padre).

---

### Ventajas del Particionamiento:
* **Pruning de Particiones (*Partition Pruning*):** El optimizador ignora completamente las particiones que no contienen los datos buscados según el `WHERE`, reduciendo drásticamente el espacio de búsqueda.
* **Mantenimiento Masivo Rápido:** En lugar de ejecutar un `DELETE FROM ordenes WHERE fecha < '2020-01-01'` (que satura el log de transacciones), se puede hacer un `DROP TABLE ordenes_y2019` de forma instantánea.

---

### Estrategias de Particionamiento:

1. **Por Rango (`RANGE`):** Asigna filas a particiones basándose en un rango de valores de la clave de partición (ej. por fechas o rangos numéricos). *Es el tipo más común.*
2. **Por Lista (`LIST`):** Asigna filas según una lista explícita de valores (ej. por región geográfica o país: `'AR'`, `'CL'`, `'UY'`).
3. **Por Hash (`HASH`):** Aplica una función hash a la clave de partición para distribuir uniformemente la carga entre un número fijo de particiones.

---

## 💻 Ejemplos Prácticos (Sintaxis PostgreSQL - Declarativo)

```sql
-- 1. Crear la tabla Padre Declarativa (Particionada por Rango de Fechas)
CREATE TABLE logs_sistema (
    log_id BIGINT GENERATED ALWAYS AS IDENTITY,
    fecha_evento DATE NOT NULL,
    nivel VARCHAR(10),
    mensaje TEXT
) PARTITION BY RANGE (fecha_evento);

-- 2. Crear las tablas Hijas (Particiones Físicas)
CREATE TABLE logs_y2026m01 PARTITION OF logs_sistema
    FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');

CREATE TABLE logs_y2026m02 PARTITION OF logs_sistema
    FOR VALUES FROM ('2026-02-01') TO ('2026-03-01');

CREATE TABLE logs_default PARTITION OF logs_sistema DEFAULT;

-- 3. Insertar datos (PostgreSQL los redirige automáticamente a la partición correcta)
INSERT INTO logs_sistema (fecha_evento, nivel, mensaje)
VALUES ('2026-01-15', 'ERROR', 'Fallo de conexión a la API');

-- 4. Demostración de Partition Pruning con EXPLAIN
-- El optimizador solo leerá la tabla physical 'logs_y2026m01' e ignorará el resto
EXPLAIN SELECT * FROM logs_sistema 
WHERE fecha_evento BETWEEN '2026-01-01' AND '2026-01-20';