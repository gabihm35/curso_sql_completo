# 09. Vistas (`VIEWS`) y Vistas Materializadas (`MATERIALIZED VIEWS`)

## 📖 Teoría

### 1. Vistas Estándar (`CREATE VIEW`)
Una **Vista** es una tabla virtual basada en el conjunto de resultados de una consulta SQL predefinida. No almacena datos físicamente en el disco (a excepción de la definición de la consulta en el catálogo del sistema).

#### Ventajas:
* **Simplificación:** Encapsula consultas complejas (`JOINs` múltiples, agregaciones, subconsultas) en una interfaz sencilla de consultar.
* **Seguridad:** Oculta filas o columnas sensibles restringiendo el acceso directo a las tablas base.
* **Mantenibilidad:** Si la estructura subyacente cambia, solo se modifica la vista sin romper las aplicaciones cliente.

---

### 2. Vistas Materializadas (`CREATE MATERIALIZED VIEW`)
A diferencia de una vista estándar, una **Vista Materializada** ejecuta la consulta subyacente y **guarda el resultado físicamente en disco** como si fuera una tabla real.

#### Características Clave:
* **Rendimiento Masivo:** Ideal para reportes analíticos, tableros de control (dashboards) o agregaciones sobre tablas gigantescas con millones de registros.
* **Persistencia de Datos:** La consulta no se ejecuta en tiempo real cada vez que se lee la vista; se lee directamente del disco.
* **Necesidad de Refresco:** Al cambiar los datos en las tablas base, la vista materializada **no se actualiza automáticamente**. Debe refrescarse explícitamente mediante el comando `REFRESH MATERIALIZED VIEW`.

---

## 💻 Ejemplos Prácticos (Sintaxis PostgreSQL)

### A. Creación y Uso de una Vista Estándar

```sql
-- 1. Crear una vista para simplificar el reporte de ventas por cliente
CREATE OR REPLACE VIEW vista_resumen_ventas AS
SELECT 
    c.cliente_id,
    CONCAT(c.nombre, ' ', c.apellido) AS cliente_nombre,
    COUNT(o.orden_id) AS total_ordenes,
    COALESCE(SUM(o.monto_total), 0) AS total_gastado
FROM clientes c
LEFT JOIN ordenes o ON c.cliente_id = o.cliente_id
GROUP BY c.cliente_id, c.nombre, c.apellido;

-- 2. Consultar la vista como si fuera una tabla normal
SELECT * 
FROM vista_resumen_ventas
WHERE total_gastado > 5000.00
ORDER BY total_gastado DESC;
```

### B. Creación y Refresco de una Vista Materializada

```sql
-- 1. Crear una vista materializada para métricas mensuales pesadas
CREATE MATERIALIZED VIEW mv_metricas_mensuales AS
SELECT 
    EXTRACT(YEAR FROM fecha_orden) AS anio,
    EXTRACT(MONTH FROM fecha_orden) AS mes,
    p.categoria_id,
    SUM(d.cantidad * d.precio_unitario) AS ingresos_totales
FROM ordenes o
INNER JOIN detalle_ordenes d ON o.orden_id = d.orden_id
INNER JOIN productos p ON d.producto_id = p.producto_id
GROUP BY 1, 2, 3;

-- 2. Consultar la vista materializada (Lectura ultra rápida)
SELECT * FROM mv_metricas_mensuales WHERE anio = 2026;

-- 3. Refrescar los datos persistidos tras nuevas inserciones o actualizaciones
REFRESH MATERIALIZED VIEW mv_metricas_mensuales;

-- Refresco concurrente (permite lecturas continuas mientras se actualiza, requiere índice único)
-- CREATE UNIQUE INDEX idx_mv_metricas ON mv_metricas_mensuales(anio, mes, categoria_id);
-- REFRESH MATERIALIZED VIEW CONCURRENTLY mv_metricas_mensuales;