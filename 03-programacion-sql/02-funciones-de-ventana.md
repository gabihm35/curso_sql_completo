# 02. Funciones de Ventana (*Window Functions*): `ROW_NUMBER`, `RANK`, `DENSE_RANK` y `PARTITION BY`

## 📖 Teoría

A diferencia de las funciones de agregación tradicionales (`GROUP BY`) que colapsan las filas para devolver una sola fila por grupo, las **Funciones de Ventana** realizan cálculos sobre un conjunto de filas relacionadas (la "ventana"), pero **manteniendo la identidad de cada fila individual** en el resultado final.

### Cláusula Básica: `OVER()`
Toda función de ventana requiere la cláusula `OVER()`, la cual define la "ventana" o partición de datos sobre la cual operará la función.

* **`PARTITION BY`:** Divide el conjunto de resultados en particiones o subgrupos independientes (funciona como un `GROUP BY`, pero sin colapsar las filas). Si se omite, la ventana es la tabla entera.
* **`ORDER BY`:** Define el orden en el que se procesan o enumeran las filas dentro de cada partición.

---

### Funciones de Enumeración y Clasificación:

1. **`ROW_NUMBER()`:** Asigna un número entero secuencial e incremental único (1, 2, 3...) a cada fila dentro de la partición, según el orden especificado. **No genera empates ni saltos.**
2. **`RANK()`:** Asigna una posición jerárquica a cada fila. Si encuentra valores idénticos en el ordenamiento, les otorga el mismo rango (empate) y **salta los números de posición siguientes** para compensar (ej. 1, 2, 2, 4).
3. **`DENSE_RANK()`:** Funciona igual que `RANK()`, asigna el mismo rango a valores idénticos pero **NO genera saltos en la secuencia** (ej. 1, 2, 2, 3).

---

## 💻 Ejemplos Prácticos

### A. Comparación Práctica: `ROW_NUMBER` vs `RANK` vs `DENSE_RANK`

Supongamos que tenemos una tabla con los salarios de los empleados:

```sql
SELECT 
    empleado_id,
    departamento_id,
    salario,
    ROW_NUMBER() OVER (ORDER BY salario DESC) AS row_num,
    RANK()       OVER (ORDER BY salario DESC) AS rank_pos,
    DENSE_RANK() OVER (ORDER BY salario DESC) AS dense_rank_pos
FROM empleados;
```
/*
RESULTADO DE LA COMPARACIÓN (Notar la diferencia en los salarios duplicados de 3000):
+-------------+-----------------+---------+---------+----------+----------------+
| empleado_id | departamento_id | salario | row_num | rank_pos | dense_rank_pos |
+-------------+-----------------+---------+---------+----------+----------------+
| 105         | 1               | 5000    | 1       | 1        | 1              |
| 102         | 2               | 4000    | 2       | 2        | 2              |
| 101         | 1               | 3000    | 3       | 3        | 3              |
| 104         | 2               | 3000    | 4       | 3        | 3              |  <-- Empate
| 103         | 1               | 2000    | 5       | 5        | 4              |  <-- RANK salta a 5, DENSE_RANK sigue a 4
+-------------+-----------------+---------+---------+----------+----------------+
*/

### Uso de PARTITION BY (Agrupar cálculos por categoría)
- Enumerar a los empleados mejor pagados dentro de cada departamento por separado:

```sql
SELECT 
    departamento_id,
    nombre,
    salario,
    ROW_NUMBER() OVER (
        PARTITION BY departamento_id 
        ORDER BY salario DESC
    ) AS ranking_depto
FROM empleados;
```

### Caso de Uso Avanzado: Top N por Grupo
- Obtener el salario más alto de cada departamento combinando una CTE con DENSE_RANK():

```sql
WITH ranking_salarios AS (
    SELECT 
        departamento_id,
        nombre,
        salario,
        DENSE_RANK() OVER (
            PARTITION BY departamento_id 
            ORDER BY salario DESC
        ) AS posicion
    FROM empleados
)
SELECT departamento_id, nombre, salario
FROM ranking_salarios
WHERE posicion = 1; -- Solo los puestos #1 de cada departamento
```