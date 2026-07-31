# 03. Normalización de Bases de Datos (1FN, 2FN, 3FN)

## 📖 Teoría

La **Normalización** es la técnica formal de diseño relacional que consiste en aplicar un conjunto de reglas (llamadas **Formas Normales**) para organizar las columnas y tablas de una base de datos.

### Objetivos Principales:
1. **Eliminar la redundancia de datos:** Evitar guardar la misma información repetida en múltiples filas.
2. **Prevenir anomalías en la manipulación (DML):**
   - *Anomalía de Inserción:* No poder guardar cierta información porque falta otra no relacionada.
   - *Anomalía de Actualización:* Modificar un dato en una fila y dejarlo desactualizado en otras.
   - *Anomalía de Borrado:* Perder información valiosa accidentalmente al eliminar un registro secundario.

---

### Las Tres Primeras Formas Normales (1FN, 2FN, 3FN)

#### 1. Primera Forma Normal (1FN) - *Atomicidad*
* **Regla 1:** Cada columna debe contener un **único valor atómico** (no se permiten listas de valores, arrays o textos separados por comas en una sola celda).
* **Regla 2:** No deben existir grupos repetitivos de columnas (ej. `telefono_1`, `telefono_2`, `telefono_3`).
* **Regla 3:** Debe existir una Clave Primaria (`PRIMARY KEY`).

#### 2. Segunda Forma Normal (2FN) - *Dependencia Funcional Completa*
* **Requisito previo:** Cumplir con la 1FN.
* **Regla:** Todos los atributos que no sean clave deben depender funcionalmente de la **totalidad de la Clave Primaria**, no solo de una parte de ella.
* *(Aplica principalmente a tablas que tienen Claves Primarias Compuestas)*.

#### 3. Tercera Forma Normal (3FN) - *Sin Dependencias Transitivas*
* **Requisito previo:** Cumplir con la 2FN.
* **Regla:** Ningún atributo no clave debe depender de otro atributo no clave. Todos los atributos deben depender **única y exclusivamente de la Clave Primaria**.
* *Frase célebre de las bases de datos:* *"Cada campo debe depender de la clave, de toda la clave, y de nada más que de la clave"*.

---

## 💻 Ejemplos Prácticos de Proceso de Normalización

### Tabla Original Desnormalizada (Con anomalías de diseño):

| Factura_ID | Fecha | Cliente_Nombre | Cliente_Ciudad | Productos_Comprados |
| :--- | :--- | :--- | :--- | :--- |
| 1001 | 2026-07-01 | Carlos Pérez | Córdoba | Laptop (1200), Mouse (25) |
| 1002 | 2026-07-02 | Ana Gómez | Mendoza | Teclado (80) |

---

### Paso 1: Aplicar 1FN (Garantizar valores atómicos)
Separamos los valores no atómicos de `Productos_Comprados` en filas individuales:

| Factura_ID | Fecha | Cliente_Nombre | Cliente_Ciudad | Producto | Precio |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1001 | 2026-07-01 | Carlos Pérez | Córdoba | Laptop | 1200.00 |
| 1001 | 2026-07-01 | Carlos Pérez | Córdoba | Mouse | 25.00 |
| 1002 | 2026-07-02 | Ana Gómez | Mendoza | Teclado | 80.00 |

*Clave Primaria Compuesta:* `(Factura_ID, Producto)`.

---

### Paso 2: Aplicar 2FN (Eliminar dependencias parciales)
`Fecha`, `Cliente_Nombre` y `Cliente_Ciudad` dependen únicamente de `Factura_ID`, no del `Producto`. Por lo tanto, los separamos en su propia tabla `Facturas`:

#### Tabla 1: `Facturas`
| Factura_ID (PK) | Fecha | Cliente_Nombre | Cliente_Ciudad |
| :--- | :--- | :--- | :--- |
| 1001 | 2026-07-01 | Carlos Pérez | Córdoba |
| 1002 | 2026-07-02 | Ana Gómez | Mendoza |

#### Tabla 2: `Detalle_Facturas`
| Factura_ID (FK) | Producto | Precio |
| :--- | :--- | :--- |
| 1001 | Laptop | 1200.00 |
| 1001 | Mouse | 25.00 |
| 1002 | Teclado | 80.00 |

---

### Paso 3: Aplicar 3FN (Eliminar dependencias transitivas)
En la tabla `Facturas`, `Cliente_Ciudad` depende de `Cliente_Nombre`, el cual no es Clave Primaria (dependencia transitiva: `Factura_ID` $\rightarrow$ `Cliente_Nombre` $\rightarrow$ `Cliente_Ciudad`). 

Separamos los datos del cliente a una entidad independiente `Clientes`:

#### Tabla 1: `Clientes`
| Cliente_ID (PK) | Nombre | Ciudad |
| :--- | :--- | :--- |
| 1 | Carlos Pérez | Córdoba |
| 2 | Ana Gómez | Mendoza |

#### Tabla 2: `Facturas` (Relacionada con Clientes)
| Factura_ID (PK) | Fecha | Cliente_ID (FK) |
| :--- | :--- | :--- |
| 1001 | 2026-07-01 | 1 |
| 1002 | 2026-07-02 | 2 |

#### Tabla 3: `Detalle_Facturas`
| Factura_ID (FK) | Producto_ID (FK) | Precio |
| :--- | :--- | :--- |
| 1001 | 101 | 1200.00 |
| 1001 | 102 | 25.00 |
| 1002 | 103 | 80.00 |

---

## ✏️ Ejercicios Prácticos

1. **Identificación de Violación de 1FN:** Observa la columna `habilidades` en esta fila de tabla: `ID: 1 | Empleado: Juan | Habilidades: 'Python, SQL, Docker'`. Explica por qué viola la 1FN y cómo la reestructurarías.
2. **Normalización a 3FN:** Considera la siguiente tabla desnormalizada:  
   `Empleados (empleado_id, nombre, departamento_id, departamento_nombre, ubicacion_departamento)`.  
   Identifica qué forma normal está violando esta estructura y escribe los nombres de las dos tablas resultantes tras aplicar la 3FN.