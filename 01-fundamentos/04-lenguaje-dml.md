### Archivo 3: `01-fundamentos/03-lenguaje-dml.md`

# 03. Lenguaje de Manipulación de Datos (DML)

## 📖 Teoría

El lenguaje **DML** se utiliza para insertar, consultar, modificar y eliminar filas almacenadas en las tablas.

* **`INSERT INTO`:** Inserta uno o varios registros en una tabla.
* **`SELECT`:** Recupera filas y columnas almacenadas.
* **`UPDATE`:** Modifica los datos de uno o varios registros existentes.
* **`DELETE FROM`:** Elimina filas específicas según una condición.

> ⚠️ **Regla de Seguridad:** Siempre ejecuta `UPDATE` y `DELETE` acompañados de la cláusula `WHERE`. De lo contrario, afectarás a la totalidad de las filas de la tabla.

---

## 💻 Ejemplos Prácticos

```sql
-- 1. Insertar registros
INSERT INTO categorias (nombre) 
VALUES ('Electrónica'), ('Hogar');

INSERT INTO productos (nombre, precio_unitario, stock, categoria_id) 
VALUES 
    ('Monitor 24 pulgadas', 220.00, 15, 1),
    ('Teclado Mecánico', 85.50, 30, 1),
    ('Cafetera Expresso', 110.00, 10, 2);

-- 2. Consultar registros
SELECT producto_id, nombre, precio_unitario 
FROM productos;

-- 3. Actualizar registros específicos
UPDATE productos 
SET precio_unitario = 79.99, stock = 25 
WHERE producto_id = 2;

-- 4. Eliminar registros específicos
DELETE FROM productos 
WHERE producto_id = 3;