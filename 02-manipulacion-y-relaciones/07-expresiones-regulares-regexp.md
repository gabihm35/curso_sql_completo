# 08. Búsqueda y Filtrado Avanzado con Expresiones Regulares (`REGEXP` / `~`)

## 📖 Teoría

Mientras que el operador `LIKE` permite búsquedas de patrones simples mediante los comodines `%` y `_`, el operador **`REGEXP`** (o `RLIKE` en MySQL, o `~` en PostgreSQL) permite evaluar **Expresiones Regulares (Regex)** completas dentro de la cláusula `WHERE`.

### Símbolos Regex Más Comunes:

* **`^`**: Indica el **inicio** de la cadena (ej. `^A` busca textos que comiencen con A).
* **`$`**: Indica el **final** de la cadena (ej. `com$` busca textos que terminen en "com").
* **`.`**: Coincide con **cualquier carácter individual**.
* **`[abc]`**: Coincide con cualquiera de los caracteres entre corchetes (ej. `[aeiou]` busca vocales).
* **`[a-z]` / `[0-9]`**: Define un **rango** de caracteres o números.
* **`|`**: Operador **OR** lógico (ej. `cat|dog` busca "cat" o "dog").
* **`*`**: Cero o más repeticiones del carácter anterior.
* **`+`**: Una o más repeticiones del carácter anterior.

---

## 💻 Ejemplos Prácticos

> **Nota de Sintaxis:** En MySQL/MariaDB se usa `REGEXP` o `RLIKE`. En PostgreSQL se utiliza el operador `~` (sensible a mayúsculas) o `~*` (insensible a mayúsculas).

```sql
-- 1. Buscar emails que terminen específicamente en .com o .org (Sintaxis MySQL)
SELECT usuario_id, email
FROM usuarios
WHERE email REGEXP '\.(com|org)$';

-- 2. Validar cadenas que comiencen con las letras 'A', 'B' o 'C'
SELECT producto_id, codigo_sku
FROM productos
WHERE codigo_sku REGEXP '^[ABC]';

-- 3. Identificar números de teléfono que contengan exactamente solo dígitos
SELECT cliente_id, telefono
FROM clientes
WHERE telefono REGEXP '^[0-9]+$';

-- 4. Ejemplo equivalente en PostgreSQL (usando el operador ~*)
SELECT usuario_id, nombre
FROM usuarios
WHERE nombre ~* '^(ana|carlos|laura)';