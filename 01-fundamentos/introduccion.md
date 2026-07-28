---

## 2. Resumen del Tema 1: Introducción a RDBMS y Lenguaje SQL

Este contenido está listo para que lo guardes dentro de tu carpeta `/01-fundamentos/01-introduccion.md`.

---

# 📖 Módulo 1: Introducción a SQL y RDBMS

## 1. Conceptos Clave
Un **RDBMS** (*Relational Database Management System*) es un sistema de gestión que organiza los datos en tablas estructuradas (filas y columnas) conectadas entre sí mediante relaciones de clave primaria (`PRIMARY KEY`) y clave foránea (`FOREIGN KEY`).

SQL (*Structured Query Language*) se divide en varias subcategorías de comandos:
- **DDL (Data Definition Language):** Define y modifica la estructura de la base de datos (`CREATE`, `ALTER`, `DROP`, `TRUNCATE`).
- **DML (Data Manipulation Language):** Manipula los datos almacenados (`SELECT`, `INSERT`, `UPDATE`, `DELETE`).
- **DCL (Data Control Language):** Gestiona permisos y accesos (`GRANT`, `REVOKE`).
- **TCL (Transaction Control Language):** Administra transacciones (`COMMIT`, `ROLLBACK`, `SAVEPOINT`).

---

## 2. Sintaxis y Ejemplos Prácticos

### A. Crear una Tabla (DDL)
Definimos una tabla para gestionar empleados en una empresa:

```sql
CREATE TABLE empleados (
    empleado_id SERIAL PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    apellido VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    salario NUMERIC(10, 2) CHECK (salario > 0),
    fecha_contratacion DATE DEFAULT CURRENT_DATE
);
```

### B. Insertar Datos (DML)

INSERT INTO empleados (nombre, apellido, email, salario, fecha_contratacion)
VALUES 
    ('Laura', 'Gómez', 'laura.gomez@email.com', 3500.00, '2025-01-15'),
    ('Carlos', 'Pérez', 'carlos.perez@email.com', 4200.50, '2024-11-01');

### C. Consultar y Filtrar Registros (DML)

SELECT 
    nombre, 
    apellido, 
    salario 
FROM empleados
WHERE salario >= 4000.00;

### Actualizar y Eliminar Registros (DML)

-- Actualizar el salario de un empleado específico
UPDATE empleados
SET salario = salario * 1.10
WHERE empleado_id = 1;

-- Eliminar un empleado específico
DELETE FROM empleados
WHERE empleado_id = 2;

