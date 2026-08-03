# 🗄️ SQL Mastery Roadmap: De Cero a Avanzado

¡Bienvenido/a a mi repositorio de aprendizaje de SQL! Este espacio reúne la documentación, ejercicios prácticos y proyectos desarrollados durante mi trayecto de aprendizaje, abarcando desde consultas básicas hasta optimización, programación procedural y despliegues en la nube.

## 📚 Índice de Contenidos

### 1. Fundamentos de SQL
- [x] Introducción a bases de datos relacionales (RDBMS)
- [x] Diseño y modelado DER y Cardinalidad
- [x] Normalizacion de bases de datos
- [x] Lenguaje DDL (`CREATE`, `ALTER`, `DROP`)
- [x] Lenguaje DML (`SELECT`, `INSERT`, `UPDATE`, `DELETE`)
- [x] Filtrado y ordenamiento (`WHERE`, `ORDER BY`, `LIKE`, `BETWEEN`)
- [x] Funciones de agregación y agrupamiento (`GROUP BY`, `HAVING`)

### 2. Manipulación y Relaciones Avanzadas
- [x] Combinación de tablas (`INNER JOIN`, `LEFT/RIGHT JOIN`, `FULL JOIN`)
- [x] Subconsultas (Correlacionadas y No correlacionadas)
- [x] Operadores de conjuntos (`UNION`, `INTERSECT`, `EXCEPT`)
- [x] Expresiones Comunes de Tabla (CTEs) y CTEs Recursivas
- [x] Funciones Escalares y Transformación (`UPPER`, `SUBSTR`, `CEILING`, `ROUND`, `FLOOR`,`CAST`, `Funciones FECHA`, `CONCAT`, `LENGTH`, `LEFT`, `RIGHT`)
- [x] Funciones de Fecha (`CURDATE`, `DATE_ADD`)
- [x] Expresiones regulares regexp (`REGEXP`, `RLIKE`)
- [x] Vistas y Vistas Materializadas (`VIEWS`, `MATERIALIZED VIEWS`)

### 3. Programación en SQL (PL/SQL, PL/pgSQL)
- [x] Variables y control de flujo (`IF`, `CASE`)
- [x] Funciones de ventana (`PARTITION BY`, `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`)
- [x] Funciones y Procedimientos Almacenados (*Stored Procedures*)
- [x] Manejo de transacciones y control de concurrencia (`BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT`)
- [x] Triggers y Automatización de eventos


### 4. Optimizador y Performance
- [x] Diseño de índices y estrategias de indexación (B-Tree, Hash, etc.)
- [x] Análisis de planes de ejecución (`EXPLAIN / EXPLAIN ANALYZE`)
- [x] Técnicas de optimización de consultas (*Query Tuning*)
- [x] Particionamiento de tablas

### 5. SQL en la Nube (AWS & RDS)
- [x] Aprovisionamiento y configuración de AWS RDS
- [x] Conexión remota y gestión de seguridad (Security Groups, VPC)
- [x] Automatización de copias de seguridad y replicación
- [x] Monitoreo de métricas con CloudWatch

---

## 🛠️ Herramientas Utilizadas

- **Base de Datos Principal:** PostgreSQL / MySQL / AWS RDS
- **Clientes GUI:** DBeaver / pgAdmin
- **Cloud:** Amazon Web Services (AWS RDS)
- **Control de Versiones:** Git & GitHub

---

## 📂 Estructura del Repositorio

```text
.
├── 01-fundamentos/
│   ├── 01-introduccion-rdbms.md
│   └── ejercicios/
├── 02-relaciones-y-joins/
├── 03-programacion-sql/
├── 04-optimizacion/
└── 05-aws-rds/