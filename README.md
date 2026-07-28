# 🗄️ SQL Mastery Roadmap: De Cero a Avanzado

¡Bienvenido/a a mi repositorio de aprendizaje de SQL! Este espacio reúne la documentación, ejercicios prácticos y proyectos desarrollados durante mi trayecto de aprendizaje, abarcando desde consultas básicas hasta optimización, programación procedural y despliegues en la nube.

---

## 📌 Estado del Curso

- **Inicio:** July 2026
- **Estado:** 🟡 En progreso
- **Base de Datos Principal:** PostgreSQL / MySQL / AWS RDS

---

## 📚 Índice de Contenidos

### 1. Fundamentos de SQL
- [x] Introducción a bases de datos relacionales (RDBMS)
- [x] Lenguaje DDL (`CREATE`, `ALTER`, `DROP`)
- [x] Lenguaje DML (`SELECT`, `INSERT`, `UPDATE`, `DELETE`)
- [x] Filtrado y ordenamiento (`WHERE`, `ORDER BY`, `LIKE`, `BETWEEN`)
- [x] Funciones de agregación y agrupamiento (`GROUP BY`, `HAVING`)

### 2. Manipulación y Relaciones Avanzadas
- [x] Combinación de tablas (`INNER JOIN`, `LEFT/RIGHT JOIN`, `FULL JOIN`)
- [x] Subconsultas (Correlacionadas y No correlacionadas)
- [x] Operadores de conjuntos (`UNION`, `INTERSECT`, `EXCEPT`)
- [x] Expresiones Comunes de Tabla (CTEs) y CTEs Recursivas

### 3. Programación en SQL (PL/SQL, PL/pgSQL)
- [ ] Variables y control de flujo (`IF`, `CASE`, `LOOP`)
- [ ] Funciones y Procedimientos Almacenados (*Stored Procedures*)
- [ ] Triggers y Automatización de eventos
- [ ] Manejo de transacciones y control de concurrencia (`BEGIN`, `COMMIT`, `ROLLBACK`)

### 4. Optimizador y Performance
- [ ] Diseño de índices y estrategias de indexación (B-Tree, Hash, etc.)
- [ ] Análisis de planes de ejecución (`EXPLAIN / EXPLAIN ANALYZE`)
- [ ] Técnicas de optimización de consultas (*Query Tuning*)
- [ ] Particionamiento de tablas

### 5. SQL en la Nube (AWS & RDS)
- [ ] Aprovisionamiento y configuración de AWS RDS
- [ ] Conexión remota y gestión de seguridad (Security Groups, VPC)
- [ ] Automatización de copias de seguridad y replicación
- [ ] Monitoreo de métricas con CloudWatch

---

## 🛠️ Herramientas Utilizadas

- **SGBD:** PostgreSQL / MySQL
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