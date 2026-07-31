# 04. Transacciones y Control de Concurrencia (`BEGIN`, `COMMIT`, `ROLLBACK`)

## 📖 Teoría

Una **Transacción** es una unidad lógica de trabajo que agrupa una o más sentencias SQL (`INSERT`, `UPDATE`, `DELETE`). Todas las instrucciones dentro de una transacción se ejecutan como un único bloque indivisible: **o se aplican todas con éxito, o no se aplica ninguna**.

---

### Propiedades ACID
Para garantizar la integridad y confiabilidad de los datos, todo sistema transaccional debe cumplir con el principio **ACID**:

1. **Atomicidad (*Atomicity*):** Todo o nada. Si falla una sola instrucción del bloque, se revierten todos los cambios realizados hasta ese momento.
2. **Consistencia (*Consistency*):** La base de datos pasa de un estado válido a otro estado válido, respetando todas las reglas e integridad referencial (`FK`, `CHECK`, `UNIQUE`).
3. **Aislamiento (*Isolation*):** Las transacciones concurrentes (ejecutadas al mismo tiempo por distintos usuarios) no se interfieren entre sí.
4. **Durabilidad (*Durability*):** Una vez que una transacción se confirma (`COMMIT`), sus cambios son permanentes y no se perderán incluso si ocurre un fallo en el sistema o corte de energía.

---

### Comandos de Control Transaccional (TCL):

* **`BEGIN` / `START TRANSACTION`:** Inicia explícitamente el bloque transaccional.
* **`COMMIT`:** Confirma y guarda permanentemente todos los cambios en el disco.
* **`ROLLBACK`:** Deshace y cancela todos los cambios realizados durante la transacción actual, volviendo al estado anterior al `BEGIN`.
* **`SAVEPOINT nombre`:** Crea un punto de restauración intermedio dentro de la transacción.
* **`ROLLBACK TO SAVEPOINT nombre`:** Revierte únicamente los cambios realizados después del punto de guardado especificado.

---

## 💻 Ejemplos Prácticos

### A. Transacción Exitosa con `COMMIT`

```sql
BEGIN;

-- Paso 1: Crear la cabecera de una orden
INSERT INTO ordenes (cliente_id, fecha_orden, total)
VALUES (10, CURRENT_TIMESTAMP, 150.00);

-- Paso 2: Descontar el stock del producto vendido
UPDATE productos
SET stock = stock - 1
WHERE producto_id = 50;

-- Paso 3: Confirmar los cambios de forma definitiva
COMMIT;
```

### B. Transacción con Reversión Controlada (ROLLBACK)

```sql
BEGIN;

UPDATE cuentas 
SET saldo = saldo - 1000.00 
WHERE cuenta_id = 1;

-- Simulación de error: La cuenta destino no existe o falló la validación
-- Cancelamos toda la operación para evitar fuga de dinero
ROLLBACK; 

-- El saldo de la cuenta 1 vuelve a su valor original intacto.
```

### Uso de Puntos de Guardado (SAVEPOINT)

```sql
BEGIN;

INSERT INTO clientes (nombre, email) VALUES ('Pedro Picapiedra', 'pedro@email.com');
SAVEPOINT cliente_registrado;

-- Operación secundaria riesgosa
INSERT INTO suscripciones (cliente_id, plan) VALUES (99999, 'VIP'); 

-- Si falla la suscripción, revertimos solo hasta el punto de guardado
ROLLBACK TO SAVEPOINT cliente_registrado;

-- El cliente se conserva, pero el intento de suscripción fallido se descarta
COMMIT;
```