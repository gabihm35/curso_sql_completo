# 01. Estructuras de Control de Flujo (`CASE` e `IF`)

## 📖 Teoría

Las estructuras condicionales permiten evaluar reglas de negocio e incorporar lógica ramificada dentro de consultas o scripts procedurales en bases de datos.

### 1. La Expresión `CASE` (Estándar SQL)
Es **estándar en todos los motores de bases de datos** (PostgreSQL, MySQL, SQL Server, Oracle, SQLite) y se utiliza dentro de declaraciones `SELECT`, `WHERE`, `UPDATE` o `ORDER BY`.

Existen dos sintaxis de `CASE`:
* **`CASE` Simple:** Evalúa una única columna contra valores específicos.
* **`CASE` Buscado (*Searched CASE*):** Evalúa múltiples condiciones booleanas independientes en cada bloque `WHEN`.

### 2. La Sentencia `IF` (Procedural / Motor Específico)
* En SQL ANSI estándar no existe un `IF` dentro de una consulta `SELECT`.
* En **PL/pgSQL (PostgreSQL)**, **T-SQL (SQL Server)** o **PL/SQL (Oracle)**, `IF` es un bloque procedural utilizado dentro de **Funciones y Procedimientos Almacenados (*Stored Procedures*)**.
* Nota en MySQL: Existe la función escalar `IF(condicion, valor_si_verdadero, valor_si_falso)` para consultas simples, pero para mantener compatibilidad universal se recomienda la expresión `CASE`.

---

## 💻 Ejemplos Prácticos

### A. Uso de `CASE` en Consultas Estándar (SQL ANSI)

```sql
-- 1. CASE Buscado: Segmentación de Clientes por Mantenimiento o Compra
SELECT 
    cliente_id,
    monto_total,
    CASE 
        WHEN monto_total >= 5000.00 THEN 'Cliente VIP'
        WHEN monto_total BETWEEN 1000.00 AND 4999.99 THEN 'Cliente Frecuente'
        ELSE 'Cliente Regular'
    END AS categoria_cliente
FROM ordenes;

-- 2. CASE dentro de una sentencia UPDATE condicional
UPDATE productos
SET precio_unitario = CASE 
    WHEN categoria_id = 1 THEN precio_unitario * 1.05 -- Aumento 5% Electrónica
    WHEN categoria_id = 2 THEN precio_unitario * 1.10 -- Aumento 10% Hogar
    ELSE precio_unitario
END;
```
### Uso de IF en Bloques Procedurales (Ejemplo PL/pgSQL)

```sql
-- Crear una función almacenada con control de flujo IF / ELSE
CREATE OR REPLACE FUNCTION verificar_stock_producto(p_producto_id INT, p_cantidad_deseada INT)
RETURNS TEXT AS $$ DECLARE     v_stock_actual INT; BEGIN     -- Obtener el stock del producto     SELECT stock INTO v_stock_actual      FROM productos      WHERE producto_id = p_producto_id;      -- Evaluar la disponibilidad mediante IF     IF v_stock_actual IS NULL THEN         RETURN 'El producto no existe.';     ELSIF v_stock_actual >= p_cantidad_deseada THEN         RETURN 'Stock disponible.';     ELSE         RETURN 'Stock insuficiente. Disponibles: ' \vert{}\vert{} v_stock_actual;     END IF; END; $$ LANGUAGE plpgsql;

-- Ejecutar la función
SELECT verificar_stock_producto(1, 5);
```