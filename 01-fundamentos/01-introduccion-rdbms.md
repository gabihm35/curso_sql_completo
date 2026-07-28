# 01. Introducción a Bases de Datos Relacionales (RDBMS)

## 📖 Teoría

Un **Sistema de Gestión de Bases de Datos Relacionales (RDBMS)** es un software diseñado para almacenar, administrar y recuperar información estructurada en tablas (filas y columnas) interrelacionadas.

### Conceptos Fundamentales:
* **Tabla (Relación):** Estructura bidimensional que representa una entidad del mundo real (ej. `clientes`, `ventas`).
* **Fila (Registro / Tupla):** Representa una instancia individual dentro de la tabla.
* **Columna (Campo / Atributo):** Propiedad o caracteristica de la entidad.
* **Clave Primaria (`PRIMARY KEY`):** Identificador único e irrepetible para cada registro de una tabla.
* **Clave Foránea (`FOREIGN KEY`):** Campo que apunta a la `PRIMARY KEY` de otra tabla, estableciendo una relación formal y garantizando **integridad referencial**.

### Categorías de Comandos SQL:
1. **DDL (*Data Definition Language*):** Define la estructura de la base de datos (`CREATE`, `ALTER`, `DROP`, `TRUNCATE`).
2. **DML (*Data Manipulation Language*):** Manipula los datos guardados (`SELECT`, `INSERT`, `UPDATE`, `DELETE`).
3. **DCL (*Data Control Language*):** Gestiona permisos de usuario (`GRANT`, `REVOKE`).
4. **TCL (*Transaction Control Language*):** Controla el flujo de transacciones (`COMMIT`, `ROLLBACK`).

---

## 💻 Ejemplo Práctico

Estructura lógica de una relación 1 a N (Un departamento tiene muchos empleados):

```text
TABLA: departamentos
+-----------------+------------------+
| departamento_id | nombre           |  <-- Clave Primaria (PK)
+-----------------+------------------+
| 1               | Sistemas         |
| 2               | Finanzas         |
+-----------------+------------------+

TABLA: empleados
+-------------+--------+-----------------+
| empleado_id | nombre | departamento_id |  <-- PK: empleado_id | FK: departamento_id
+-------------+--------+-----------------+
| 101         | Ana    | 1               |  (Pertenece a Sistemas)
| 102         | Luis   | 2               |  (Pertenece a Finanzas)
+-------------+--------+-----------------+