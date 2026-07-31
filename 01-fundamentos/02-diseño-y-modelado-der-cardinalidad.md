# 02. Diseño de Bases de Datos: Diagrama Entidad-Relación (DER), Claves y Cardinalidad

## 📖 Teoría

El diseño de una base de datos comienza a nivel conceptual. Un modelo mal diseñado desde su origen provocará duplicación de datos, inconsistencias y consultas SQL lentas o imposibles de mantener.

---

### 1. Diagrama Entidad-Relación (DER / ERD)
Es una herramienta gráfica utilizada para representar la estructura lógica de una base de datos. Sus componentes principales son:

* **Entidad:** Objeto o concepto del mundo real del cual queremos guardar información (ej. `Cliente`, `Producto`, `Factura`). Se representa formalmente en las bases de datos como una **Tabla**.
* **Atributo:** Propiedad o característica de una entidad (ej. `email`, `precio`, `fecha_nacimiento`). En SQL corresponde a una **Columna**.
* **Relación:** Vínculo o asociación lógica entre dos o más entidades (ej. Un `Cliente` *realiza* un `Pedido`).

---

### 2. Claves Primarias y Foráneas

* **Clave Primaria (`PRIMARY KEY` / PK):**
  - Identificador único e irrepetible para cada fila de una tabla.
  - No puede contener valores nulos (`NOT NULL`).
  - *Ejemplo:* `cliente_id`, `dni`, `codigo_sku`.

* **Clave Foránea (`FOREIGN KEY` / FK):**
  - Campo en una tabla que apunta y hace referencia a la `PRIMARY KEY` de otra tabla.
  - Establece la **Integridad Referencial**: Impide insertar un registro con un ID de referencia que no exista previamente en la tabla madre.

---

### 3. Cardinalidad
La cardinalidad define la cantidad de instancias de una entidad que pueden asociarse con las instancias de otra entidad.

* **Uno a Uno (1 : 1):** Un registro de la Tabla A se relaciona con un único registro de la Tabla B.
  - *Ejemplo:* `Usuario` $\rightarrow$ `Perfil_Configuracion`.
* **Uno a Muchos (1 : N):** Un registro de la Tabla A se relaciona con múltiples registros de la Tabla B, pero cada registro de B pertenece a solo un registro de A. *(Es la relación más común en SQL y la FK siempre se ubica en el lado "N")*.
  - *Ejemplo:* Un `Cliente` realiza muchos `Pedidos`. Cada `Pedido` pertenece a un solo `Cliente`.
* **Muchos a Muchos (N : M):** Múltiples registros de A se relacionan con múltiples registros de B.
  - *Ejemplo:* Un `Estudiante` se inscribe en muchas `Materias`. Una `Materia` tiene muchos `Estudiantes`.
  - ⚠️ **Regla Relacional:** Las relaciones N:M **no se pueden implementar directamente en SQL**. Se deben descomponer creando una **Tabla Intermedia / Tabla de Unión** que transforme la relación en dos relaciones $1:N$.

---

## 💻 Ejemplos Prácticos en Diagramas de Texto y SQL

### A. Descomposición de una Relación Muchos a Muchos (N:M)

#### Relación Conceptual (N:M):
> `pedidos` (N) $\longleftrightarrow$ (M) `productos`  
*(Un pedido contiene varios productos, un producto está en varios pedidos)*

#### Solución Relacional con Tabla Intermedia:
```text
+----------------+       1:N       +---------------------+       N:1       +----------------+
|    pedidos     | <-------------> |  detalle_pedidos    | <-------------> |   productos    |
+----------------+                 +---------------------+                 +----------------+
| PK  pedido_id  |                 | PK  detalle_id      |                 | PK  producto_id|
|     fecha      |                 | FK  pedido_id       |                 |     nombre     |
|     cliente_id |                 | FK  producto_id     |                 |     precio     |
+----------------+                 |     cantidad        |                 +----------------+
                                   |     precio_unitario |
                                   +---------------------+
```

### Implementación DDL del Modelo Relacional en SQL.

```sql
-- 1. Tabla Madre (Lado 1)
CREATE TABLE clientes (
    cliente_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE NOT NULL
);

-- 2. Tabla Hija con Clave Foránea (Lado N - Relación 1:N)
CREATE TABLE pedidos (
    pedido_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    fecha_pedido TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    cliente_id INT NOT NULL,
    
    -- Definición explícita de la Integridad Referencial (FK)
    CONSTRAINT fk_pedidos_clientes 
        FOREIGN KEY (cliente_id) 
        REFERENCES clientes(cliente_id)
        ON DELETE RESTRICT
);

-- 3. Tabla Intermedia para romper la relación N:M
CREATE TABLE detalle_pedidos (
    pedido_id INT NOT NULL,
    producto_id INT NOT NULL,
    cantidad INT NOT NULL CHECK (cantidad > 0),
    precio_unitario NUMERIC(10, 2) NOT NULL,
    
    -- Clave Primaria Compuesta (combinación de ambas FKs)
    PRIMARY KEY (pedido_id, producto_id),
    
    CONSTRAINT fk_detalle_pedidos
        FOREIGN KEY (pedido_id) REFERENCES pedidos(pedido_id),
    CONSTRAINT fk_detalle_productos
        FOREIGN KEY (producto_id) REFERENCES productos(producto_id)
);
```
