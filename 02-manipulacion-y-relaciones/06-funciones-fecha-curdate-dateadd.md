# 07. Manejo de Fechas en MySQL: `CURDATE()` y `DATE_ADD()`

## 📖 Teoría

El manejo de fechas es indispensable para calcular vencimientos, filtrar transacciones recientes, determinar métricas por períodos de tiempo o automatizar alertas.

### Unidades comunes (UNIDAD):

* DAY: Días

* MONTH: Meses

* YEAR: Años

* WEEK: Semanas

* HOUR: Horas

* MINUTE: Minutos

💡 Tip: Para restar tiempo en MySQL puedes usar DATE_SUB() o simplemente pasar un valor negativo a DATE_ADD() (ej. DATE_ADD(CURDATE(), INTERVAL -7 DAY)).

### 1. `CURDATE()`
Devuelve la **fecha actual del sistema** en el momento de ejecutar la consulta, expresada en formato `'YYYY-MM-DD'` (sin incluir la hora, minutos ni segundos).

* **Sintaxis:** `CURDATE()`
* **Sinónimo habitual:** `CURRENT_DATE()` o `CURRENT_DATE`.

---

### 2. `DATE_ADD()`
Suma un **intervalo de tiempo específico** (días, meses, años, horas, etc.) a una fecha dada.

* **Sintaxis:**
```sql
  DATE_ADD(fecha_inicial, INTERVAL cantidad UNIDAD)
```

### Consultas Básicas con CURDATE() y DATE_ADD().

```sql
-- 1. Obtener la fecha actual del servidor
SELECT CURDATE() AS fecha_hoy;

-- 2. Calcular fechas futuras a partir de hoy
SELECT 
    CURDATE() AS hoy,
    DATE_ADD(CURDATE(), INTERVAL 15 DAY) AS vencimiento_15_dias,
    DATE_ADD(CURDATE(), INTERVAL 1 MONTH) AS proximo_mes,
    DATE_ADD(CURDATE(), INTERVAL 1 YEAR) AS proximo_anio;

-- 3. Calcular fechas pasadas utilizando un intervalo negativo
SELECT 
    CURDATE() AS hoy,
    DATE_ADD(CURDATE(), INTERVAL -30 DAY) AS hace_30_dias;
```
### Aplicación en Operaciones DML (SELECT, INSERT, UPDATE).

```sql
-- 1. Filtrar registros de los últimos 7 días en una tabla de ordenes
SELECT 
    orden_id, 
    cliente_id, 
    fecha_orden, 
    monto_total
FROM ordenes
WHERE fecha_orden >= DATE_ADD(CURDATE(), INTERVAL -7 DAY);

-- 2. Insertar una suscripción que vence automáticamente en 3 meses
INSERT INTO suscripciones (cliente_id, fecha_inicio, fecha_vencimiento)
VALUES (
    101, 
    CURDATE(), 
    DATE_ADD(CURDATE(), INTERVAL 3 MONTH)
);

-- 3. Actualizar la fecha de extensión de cupones vencidos
UPDATE cupones
SET fecha_expiracion = DATE_ADD(CURDATE(), INTERVAL 15 DAY)
WHERE fecha_expiracion < CURDATE() 
  AND estado = 'Activo';
```

