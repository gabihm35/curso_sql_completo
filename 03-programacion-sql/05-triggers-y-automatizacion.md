# 05. Triggers (Disparadores) y Automatización

## 📖 Teoría

Un **Trigger** (o Disparador) es un objeto de base de datos que ejecuta automáticamente una función o bloque de código SQL en respuesta a un evento específico (`INSERT`, `UPDATE` o `DELETE`) ocurrido sobre una tabla o vista.

### Momentos de Disparo (*Timing*):
* **`BEFORE`:** Se ejecuta **antes** de que los cambios se apliquen a la tabla. Ideal para validaciones de datos o modificaciones de valores antes de guardar.
* **`AFTER`:** Se ejecuta **después** de que la operación DML se ha completado en la tabla. Ideal para auditorías, réplicas o notificaciones.
* **`INSTEAD OF`:** Reemplaza la operación DML original. Se utiliza principalmente sobre vistas no actualizables.

### Nivel de Ejecución:
* **`FOR EACH ROW` (A nivel de fila):** Se dispara una vez por cada fila afectada por la consulta.
* **`FOR EACH STATEMENT` (A nivel de sentencia):** Se dispara una sola vez por consulta completa, independientemente de cuántas filas hayan sido afectadas.

### Variables Especiales en Triggers:
* **`NEW`:** Variable récord que contiene los datos de la **nueva fila** siendo insertada o actualizada (`INSERT` / `UPDATE`).
* **`OLD`:** Variable récord que contiene los datos de la **fila antigua** antes de ser modificada o eliminada (`UPDATE` / `DELETE`).

---

## 💻 Ejemplos Prácticos (Sintaxis PL/pgSQL)

En motores como PostgreSQL, la creación de un Trigger se divide en dos pasos:
1. Definir la **Función Trigger** (`RETURNS TRIGGER`).
2. Definir el **Trigger** asociado a la tabla.

### A. Trigger de Auditoría (`AFTER INSERT OR UPDATE`)

```sql
-- 1. Tabla para almacenar el historial de cambios
CREATE TABLE auditoria_precios (
    auditoria_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    producto_id INT NOT NULL,
    precio_anterior NUMERIC(10,2),
    precio_nuevo NUMERIC(10,2),
    fecha_modificacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    usuario VARCHAR(50) DEFAULT CURRENT_USER
);

-- 2. Función Trigger que evalúa e inserta el registro de auditoría
CREATE OR REPLACE FUNCTION fn_log_cambio_precio()
RETURNS TRIGGER AS $$ BEGIN     -- Verificar si el precio realmente cambió     IF OLD.precio_unitario <> NEW.precio_unitario THEN         INSERT INTO auditoria_precios (producto_id, precio_anterior, precio_nuevo)         VALUES (OLD.producto_id, OLD.precio_unitario, NEW.precio_unitario);     END IF;     RETURN NEW; END; $$ LANGUAGE plpgsql;

-- 3. Asociar el Trigger a la tabla productos
CREATE TRIGGER trg_auditar_precio_producto
AFTER UPDATE ON productos
FOR EACH ROW
EXECUTE FUNCTION fn_log_cambio_precio();
```

### B. Trigger de Validación (BEFORE INSERT OR UPDATE)

```sql
-- Función Trigger para prevenir salarios negativos o nulos
CREATE OR REPLACE FUNCTION fn_validar_salario()
RETURNS TRIGGER AS $$ BEGIN     IF NEW.salario < 0 THEN         RAISE EXCEPTION 'El salario no puede ser negativo: \%', NEW.salario;     END IF;     RETURN NEW; END; $$ LANGUAGE plpgsql;

-- Asocación del Trigger
CREATE TRIGGER trg_validar_salario_empleado
BEFORE INSERT OR UPDATE ON empleados
FOR EACH ROW
EXECUTE FUNCTION fn_validar_salario();