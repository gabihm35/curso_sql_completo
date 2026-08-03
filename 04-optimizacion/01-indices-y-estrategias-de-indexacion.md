# 01. Diseño de Índices y Estrategias de Indexación

## 📖 Teoría

Un **Índice** es una estructura de datos auxiliar ordenada (independiente de la tabla base) que permite al motor localizar filas rápidamente sin tener que escanear toda la tabla (*Sequential Scan*).

> ⚠️ **El costo de los índices:** Aunque aceleran las consultas de lectura (`SELECT`), imponen una sobrecarga de rendimiento y almacenamiento en las operaciones de escritura (`INSERT`, `UPDATE`, `DELETE`), ya que los índices deben actualizarse en cada modificación.

---

### Tipos de Índices Principales:

1. **B-Tree (Balanced Tree) - *Por Defecto*:**
   - Estructura en árbol balanceado. Mantiene los datos ordenados.
   - **Casos de uso:** Búsquedas por igualdad (`=`), rangos (`<`, `>`, `BETWEEN`), coincidencias de prefijo (`LIKE 'ABC%'`) y ordenamientos (`ORDER BY`).

2. **Hash:**
   - Utiliza una función hash para mapear valores.
   - **Casos de uso:** Exclusivamente para comparaciones de igualdad exacta (`=`). No soporta búsquedas por rango ni ordenamiento.

3. **GIN (Generalized Inverted Index):**
   - Índice invertido que mapea elementos individuales (como componentes de un JSON, palabras en Full-Text Search o arrays) con las filas que los contienen.
   - **Casos de uso:** Columnas `JSONB`, arreglos (`ARRAY`) y búsqueda de texto completo.

4. **GiST (Generalized Search Tree) / BRIN (Block Range Index):**
   - **GiST:** Utilizado para datos espaciales (GIS/PostGIS), geometrías o rangos de datos overlapping.
   - **BRIN:** Mantiene rangos min/máx por bloque de disco. Ideal para tablas de series temporales masivas (Big Data) ordenadas por fecha.

---

### Tipos de Estrategias de Indexación:
* **Índice Compuesto (Multicolumna):** Índice sobre 2 o más columnas. El orden importa: la columna utilizada con mayor frecuencia en filtros debe ir primero.
* **Índice Parcial (Condicional):** Indexa únicamente un subconjunto de filas que cumplen una condición `WHERE` (reduce espacio en disco y acelera escrituras).
* **Índice Expresivo (Basado en Funciones):** Indexa el resultado de una función aplicada a una columna (ej. `LOWER(email)`).

---

## 💻 Ejemplos Prácticos (Sintaxis PostgreSQL)

```sql
-- 1. Crear un índice B-Tree básico (por defecto)
CREATE INDEX idx_clientes_apellido ON clientes(apellido);

-- 2. Crear un índice Hash para búsquedas de igualdad exacta
CREATE INDEX idx_usuarios_token ON usuarios USING HASH (token_sesion);

-- 3. Crear un índice Compuesto
-- Eficiente para queries con: WHERE categoria_id = X AND precio_unitario > Y
CREATE INDEX idx_productos_cat_precio ON productos(categoria_id, precio_unitario);

-- 4. Crear un índice Parcial (Solo productos activos, ahorra espacio)
CREATE INDEX idx_ordenes_pendientes ON ordenes(fecha_orden)
WHERE estado = 'Pendiente';

-- 5. Crear un índice Expresivo (Permite aprovechar el índice al usar LOWER en el WHERE)
CREATE INDEX idx_usuarios_email_lower ON usuarios(LOWER(email));

-- Consulta que aprovecha el índice expresivo:
SELECT * FROM usuarios WHERE LOWER(email) = 'ejemplo@dominio.com';