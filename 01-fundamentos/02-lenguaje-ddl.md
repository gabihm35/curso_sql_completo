# 02. Lenguaje de Definición de Datos (DDL)

## 📖 Teoría

El lenguaje **DDL** permite definir, alterar o eliminar la estructura física de los objetos almacenados en el motor SQL.

* **`CREATE TABLE`:** Crea una tabla definiendo columnas, tipos de datos y restricciones (*constraints*).
* **`ALTER TABLE`:** Cambia la estructura de una tabla existente (agrega/modifica columnas o restricciones).
* **`DROP TABLE`:** Elimina una tabla por completo, eliminando su estructura y todos sus datos.
* **`TRUNCATE TABLE`:** Elimina todas las filas de una tabla de forma eficiente sin borrar la estructura.

### Restricciones Comunes (*Constraints*):
- `NOT NULL`: Impide valores nulos.
- `UNIQUE`: Asegura que todos los valores de la columna sean distintos.
- `CHECK`: Valida que los valores cumplan una condición específica.
- `DEFAULT`: Establece un valor predeterminado si no se ingresa uno.

---

## 💻 Ejemplos Prácticos

```sql
-- 1. Creación de tablas relacionales con restricciones
CREATE TABLE categorias (
    categoria_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE productos (
    producto_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    precio NUMERIC(10, 2) NOT NULL CHECK (precio > 0),
    stock INT DEFAULT 0 CHECK (stock >= 0),
    categoria_id INT,
    fecha_alta DATE DEFAULT CURRENT_DATE,
    CONSTRAINT fk_productos_categorias 
        FOREIGN KEY (categoria_id) 
        REFERENCES categorias(categoria_id)
        ON DELETE SET NULL
);

-- 2. Modificación de la estructura
ALTER TABLE productos 
ADD COLUMN codigo_sku VARCHAR(30) UNIQUE;

ALTER TABLE productos 
RENAME COLUMN precio TO precio_unitario;

-- 3. Borrado de objetos
DROP TABLE productos;