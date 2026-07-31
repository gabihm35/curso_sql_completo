# 03. Procedimientos Almacenados (*Stored Procedures*)

## 📖 Teoría

Un **Procedimiento Almacenado** (*Stored Procedure*) es un bloque de código SQL con nombre que se compila y guarda directamente en el motor de la base de datos. Puede recibir parámetros de entrada (`IN`), devolver parámetros de salida (`OUT`) y ejecutar secuencias complejas de comandos DML (`INSERT`, `UPDATE`, `DELETE`).

### Ventajas Principales:
* **Rendimiento:** Se compilan una sola vez en el servidor, reduciendo el tráfico de red respecto a enviar múltiples consultas individuales desde la aplicación.
* **Seguridad:** Permite conceder permisos a un usuario para ejecutar el procedimiento sin darle acceso directo a las tablas subyacentes.
* **Reutilización y Mantenimiento:** La lógica de negocio crítica se centraliza en la base de datos.

### Diferencia entre Función (`FUNCTION`) y Procedimiento (`PROCEDURE`):
| Característica | Función (`CREATE FUNCTION`) | Procedimiento (`CREATE PROCEDURE`) |
| :--- | :--- | :--- |
| **Valor de retorno** | **Debe** retornar un valor o conjunto de filas. | No requiere retornar un valor de forma obligatoria. |
| **Invocación** | Se llama dentro de una consulta (`SELECT mi_funcion()`). | Se llama con `CALL` (o `EXEC` / `EXECUTE`). |
| **Transacciones** | No puede gestionar transacciones internas (`COMMIT`/`ROLLBACK`). | **Puede** iniciar y finalizar transacciones (`COMMIT`/`ROLLBACK`) dentro de su cuerpo. |

---

## 💻 Ejemplos Prácticos

> **Nota de sintaxis:** El ejemplo utiliza la sintaxis estándar **PostgreSQL / PL/pgSQL** (soporta `CREATE PROCEDURE` a partir de PostgreSQL 11).

### A. Crear un Procedimiento de Transferencia con Parámetros

```sql
-- 1. Definición del procedimiento almacenado
CREATE OR REPLACE PROCEDURE realizar_transferencia(
    p_cuenta_origen INT,
    p_cuenta_destino INT,
    p_monto NUMERIC(12, 2)
)
LANGUAGE plpgsql
AS $$ DECLARE     v_saldo_origen NUMERIC(12, 2); BEGIN     -- Validar saldo de la cuenta de origen     SELECT saldo INTO v_saldo_origen      FROM cuentas      WHERE cuenta_id = p_cuenta_origen;      IF v_saldo_origen IS NULL THEN         RAISE EXCEPTION 'La cuenta de origen \% no existe.', p_cuenta_origen;     ELSIF v_saldo_origen < p_monto THEN         RAISE EXCEPTION 'Saldo insuficiente. Saldo actual: \%', v_saldo_origen;     END IF;      -- Descontar monto de la cuenta de origen     UPDATE cuentas      SET saldo = saldo - p_monto      WHERE cuenta_id = p_cuenta_origen;      -- Acreditar monto en la cuenta de destino     UPDATE cuentas      SET saldo = saldo + p_monto      WHERE cuenta_id = p_cuenta_destino;      RAISE NOTICE 'Transferencia de \% realizada con éxito de \% a \%', p_monto, p_cuenta_origen, p_cuenta_destino; END; $$;

-- 2. Invocación del procedimiento
CALL realizar_transferencia(101, 202, 500.00);