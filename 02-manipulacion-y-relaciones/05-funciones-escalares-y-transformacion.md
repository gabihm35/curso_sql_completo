# 06. Funciones Escalares, Manejo de Fechas y Conversión de Tipos

## 📖 Teoría

Las **funciones escalares** procesan valores individuales de entrada por cada fila y devuelven un único valor transformado.

### 1. Transformación de Texto:
* **`UPPER(texto)`:** Convierte una cadena de caracteres a mayúsculas.
* **`SUBSTR(texto, inicio, longitud)`** (o `SUBSTRING`): Extrae una subcadena a partir de una posición inicial (basada en índice 1) y una longitud determinada.
* **`CONCAT(cadena1, cadena2, ...)`:** Une dos o más cadenas de texto en una sola. En muchos motores (como PostgreSQL o MySQL), ignora automáticamente los valores `NULL` o los convierte en texto vacío (a diferencia del operador `||` o `+` que pueden devolver `NULL` si uno de los elementos es nulo).
* **`LENGTH(texto)`** (o `LEN` en SQL Server): Devuelve la cantidad total de caracteres contenidos en una cadena de texto.
* **`LEFT(texto, n)`:** Extrae los primeros `n` caracteres comenzando desde la **izquierda** (inicio del texto).
* **`RIGHT(texto, n)`:** Extrae los últimos `n` caracteres comenzando desde la **derecha** (final del texto).

### 2. Funciones Numéricas:
* **`CEILING(numero)`** (o `CEIL`): Redondea un número decimal hacia arriba al entero superior más cercano.
* **`ROUND(numero [, decimales])`:** Redondea un número decimal al número entero o a la cantidad de decimales especificada según la regla matemática estándar (si la parte decimal es $\ge 0.5$, redondea hacia arriba; si es $< 0.5$, redondea hacia abajo).
* **`FLOOR(numero)`:** Redondea un número hacia **abajo**, devolviendo el entero mayor que sea menor o igual al número dado (trunca los decimales hacia el piso numérico).

### 3. Conversión de Tipos de Datos:
* **`CAST(expresion AS tipo_dato)`** (o sintaxis corta `expresion::tipo_dato` en PostgreSQL): Convierte explícitamente un valor de un tipo de dato a otro (ej. de texto a entero, de flotante a fecha).

### 4. Funciones de Fecha y Tiempo:
Manejan marcas temporales, extracciones de partes de fechas y cálculos de intervalos.
* **Obtener fecha/hora actual:** `CURRENT_DATE`, `CURRENT_TIMESTAMP`, `NOW()`.
* **Extraer partes de fecha:** `EXTRACT(part FROM fecha)` (extrae año, mes, día, etc.).
* **Añadir/restar intervalos:** Operaciones con `INTERVAL` o funciones específicas según el motor (`DATEADD`, `DATEDIFF` o resta directa de fechas).

---

## 💻 Ejemplos Prácticos

```sql
-- 1. Manipulación de Texto
SELECT 
    nombre,
    UPPER(nombre) AS nombre_mayusculas,
    SUBSTR(codigo_sku, 1, 3) AS prefijo_categoria
FROM productos;

-- 2. Redondeo Numérico con CEILING, ROUND
SELECT 
    producto_id,
    precio_unitario,
    CEILING(precio_unitario) AS precio_redondeado_arriba
FROM productos;

-- Redondeo a entero más cercano
SELECT 
    precio_unitario,
    ROUND(precio_unitario) AS precio_entero
FROM productos;

-- Redondeo a una cantidad específica de decimales (ej. para calcular impuestos)
SELECT 
    monto_subtotal,
    monto_subtotal * 0.21 AS IVA_exacto,
    ROUND(monto_subtotal * 0.21, 2) AS IVA_dos_decimales
FROM facturas;

-- Casos de Uso Comunes

-- 1. Descuentos con FLOOR (ej. otorgar 1 punto por cada $10 enteros gastados)
SELECT 
    cliente_id,
    monto_compra,
    FLOOR(monto_compra / 10) AS puntos_ganados
FROM compras;

-- 2. Cálculo de promedios amigables para el usuario
SELECT 
    categoria_id,
    AVG(precio_unitario) AS promedio_exacto,
    ROUND(AVG(precio_unitario), 2) AS promedio_presentacion
FROM productos
GROUP BY categoria_id;

-- 3. Conversión de Tipos con CAST
SELECT 
    '150' AS texto_original,
    CAST('150' AS INTEGER) AS entero_convertido,
    CAST(precio_unitario AS VARCHAR) AS precio_texto,
    CAST('2026-07-29' AS DATE) AS fecha_convertida
FROM productos;

-- 4. Funciones de Fecha (Ejemplo Estándar / PostgreSQL)
SELECT 
    pedido_id,
    fecha_pedido,
    CURRENT_DATE AS fecha_actual,
    EXTRACT(YEAR FROM fecha_pedido) AS anio_pedido,
    EXTRACT(MONTH FROM fecha_pedido) AS mes_pedido,
    -- Calcular días transcurridos desde el pedido
    CURRENT_DATE - fecha_pedido AS dias_transcurridos,
    -- Calcular fecha estimada de entrega (+7 días)
    fecha_pedido + INTERVAL '7 days' AS fecha_estimada_entrega
FROM pedidos;

-- 1. Combinar nombre y apellido para crear un nombre completo
SELECT 
    nombre,
    apellido,
    CONCAT(nombre, ' ', apellido) AS nombre_completo
FROM clientes;

-- 2. Crear una dirección formal combinando múltiples campos
SELECT 
    calle,
    numero,
    ciudad,
    CONCAT(calle, ' N° ', numero, ', ', ciudad) AS direccion_completa
FROM direcciones;

-- 1. Validar la longitud de cadenas (ej. contraseñas o números de teléfono)
SELECT 
    usuario_id,
    email,
    LENGTH(contrasena_hash) AS longitud_hash
FROM usuarios
WHERE LENGTH(telefono) < 8; -- Detectar números telefónicos incompletos

-- 1. Extraer el código de país o prefijo de un número telefónico (LEFT)
SELECT 
    telefono,
    LEFT(telefono, 3) AS codigo_area
FROM clientes;

-- 2. Mostrar únicamente los últimos 4 dígitos de una tarjeta de crédito (RIGHT)
SELECT 
    cliente_id,
    numero_tarjeta,
    CONCAT('****-****-****-', RIGHT(numero_tarjeta, 4)) AS tarjeta_enmascarada
FROM pagos;

-- 3. Combinación de funciones: Generar un código de usuario automático
-- Formato: 2 primeras letras del nombre + 2 últimas letras del apellido + longitud del email
SELECT 
    nombre,
    apellido,
    CONCAT(
        UPPER(LEFT(nombre, 2)), 
        UPPER(RIGHT(apellido, 2)), 
        LENGTH(email)
    ) AS codigo_usuario
FROM usuarios;