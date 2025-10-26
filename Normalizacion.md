# Normalización de la tabla `Registro_Cursos`

## Objetivo
Aplicar los principios de **normalización de bases de datos relacionales** hasta la **Tercera Forma Normal (3NF)**, para garantizar que los datos sean **consistentes**, **no redundantes** y **fáciles de mantener**.

---

## Contexto inicial (0NF)

La universidad registra la información en una sola tabla:

| ID_Registro | Curso              | Profesor      | Email_Profesor   | Estudiantes           | Emails_Estudiantes                      | Créditos | Facultad     |
|--------------|-------------------|----------------|------------------|------------------------|------------------------------------------|-----------|--------------|
| 1            | Bases de Datos     | Ana Torres     | ana@uni.edu      | Luis, María, Jorge     | luis@uni.edu, maria@uni.edu, jorge@uni.edu | 4         | Ingeniería   |
| 2            | Programación Web   | Carlos López   | carlos@uni.edu   | Pedro, Ana, Lucía      | pedro@uni.edu, ana@uni.edu, lucia@uni.edu | 5         | Ingeniería   |

---

## Análisis inicial (0NF)

**Problemas detectados:**
1. Los campos `Estudiantes` y `Emails_Estudiantes` contienen **valores múltiples** (listas de nombres o correos).
2. La información de profesor se **repite** por cada curso.
3. No existe una **clave primaria clara** más allá de `ID_Registro`, que no garantiza unicidad lógica de los datos.
4. La tabla mezcla **entidades diferentes** (Cursos, Profesores, Estudiantes, Facultades).
 **Conclusión:** la tabla no cumple ni siquiera con la Primera Forma Normal (1NF) porque contiene **atributos no atómicos**.

---

## Primera Forma Normal (1NF)

### Reglas de la 1NF
Una tabla está en 1NF si:
- Todos los atributos contienen **valores atómicos** (no listas ni conjuntos).
- Cada registro es **único**.
- Cada campo contiene un **solo valor**.

### Aplicación

Descomponemos los valores múltiples (`Estudiantes`, `Emails_Estudiantes`) en registros separados, uno por cada estudiante inscrito.

### Estructura resultante (1NF)

| ID_Registro | Curso             | Profesor      | Email_Profesor   | Estudiante | Email_Estudiante    | Créditos | Facultad   |
|--------------|------------------|---------------|------------------|-------------|---------------------|-----------|------------|
| 1            | Bases de Datos   | Ana Torres    | ana@uni.edu      | Luis        | luis@uni.edu        | 4         | Ingeniería |
| 1            | Bases de Datos   | Ana Torres    | ana@uni.edu      | María       | maria@uni.edu       | 4         | Ingeniería |
| 1            | Bases de Datos   | Ana Torres    | ana@uni.edu      | Jorge       | jorge@uni.edu       | 4         | Ingeniería |
| 2            | Programación Web | Carlos López  | carlos@uni.edu   | Pedro       | pedro@uni.edu       | 5         | Ingeniería |
| 2            | Programación Web | Carlos López  | carlos@uni.edu   | Ana         | ana@uni.edu         | 5         | Ingeniería |
| 2            | Programación Web | Carlos López  | carlos@uni.edu   | Lucía       | lucia@uni.edu       | 5         | Ingeniería |

Ahora los datos están atómicos, pero todavía hay **redundancia**: los datos del profesor y del curso se repiten.

---

## Segunda Forma Normal (2NF)

### Reglas de la 2NF
Una tabla está en 2NF si:
- Está en **1NF**.
- **Todos los atributos no clave** dependen **completamente** de la clave primaria (no existen dependencias parciales).

### Aplicación

Creamos tablas separadas para **Cursos**, **Profesores** y **Estudiantes**.  
Cada curso se asocia con un profesor, y cada estudiante se inscribe en cursos.

### Estructura resultante (2NF)

#### Tabla: `Profesor`
| ID_Profesor | Nombre        | Email_Profesor   |
|--------------|---------------|------------------|
| 1            | Ana Torres    | ana@uni.edu      |
| 2            | Carlos López  | carlos@uni.edu   |

#### Tabla: `Curso`
| ID_Curso | Nombre_Curso     | Créditos | Facultad     | ID_Profesor |
|-----------|------------------|-----------|--------------|--------------|
| 1         | Bases de Datos   | 4         | Ingeniería   | 1            |
| 2         | Programación Web | 5         | Ingeniería   | 2            |

#### Tabla: `Estudiante`
| ID_Estudiante | Nombre  | Email_Estudiante   |
|----------------|----------|--------------------|
| 1              | Luis     | luis@uni.edu       |
| 2              | María    | maria@uni.edu      |
| 3              | Jorge    | jorge@uni.edu      |
| 4              | Pedro    | pedro@uni.edu      |
| 5              | Ana      | ana@uni.edu        |
| 6              | Lucía    | lucia@uni.edu      |

#### Tabla: `Inscripción`
| ID_Inscripción | ID_Estudiante | ID_Curso |
|----------------|----------------|-----------|
| 1              | 1              | 1         |
| 2              | 2              | 1         |
| 3              | 3              | 1         |
| 4              | 4              | 2         |
| 5              | 5              | 2         |
| 6              | 6              | 2         |

Ahora cada dato depende totalmente de su clave.  
El curso depende del profesor, y la relación entre curso y estudiante se maneja con una tabla intermedia.

---

## Tercera Forma Normal (3NF)

### Reglas de la 3NF
Una tabla está en 3NF si:
- Está en **2NF**.
- No existen **dependencias transitivas** (es decir, ningún campo no clave depende de otro campo no clave).

### Aplicación

En este caso, la **Facultad** depende del curso, no del profesor ni del estudiante.  
Pero si varios cursos pertenecen a la misma facultad, conviene crear una tabla separada.

### Estructura final (3NF)

#### Tabla: `Facultad`
| ID_Facultad | Nombre_Facultad |
|--------------|----------------|
| 1            | Ingeniería      |

#### Tabla: `Profesor`
| ID_Profesor | Nombre        | Email_Profesor   |
|--------------|---------------|------------------|
| 1            | Ana Torres    | ana@uni.edu      |
| 2            | Carlos López  | carlos@uni.edu   |

#### Tabla: `Curso`
| ID_Curso | Nombre_Curso     | Créditos | ID_Facultad | ID_Profesor |
|-----------|------------------|-----------|--------------|--------------|
| 1         | Bases de Datos   | 4         | 1            | 1            |
| 2         | Programación Web | 5         | 1            | 2            |

#### Tabla: `Estudiante`
| ID_Estudiante | Nombre  | Email_Estudiante   |
|----------------|----------|--------------------|
| 1              | Luis     | luis@uni.edu       |
| 2              | María    | maria@uni.edu      |
| 3              | Jorge    | jorge@uni.edu      |
| 4              | Pedro    | pedro@uni.edu      |
| 5              | Ana      | ana@uni.edu        |
| 6              | Lucía    | lucia@uni.edu      |

#### Tabla: `Inscripción`
| ID_Inscripción | ID_Estudiante | ID_Curso |
|----------------|----------------|-----------|
| 1              | 1              | 1         |
| 2              | 2              | 1         |
| 3              | 3              | 1         |
| 4              | 4              | 2         |
| 5              | 5              | 2         |
| 6              | 6              | 2         |

---

## Conclusión

| Etapa | Problemas resueltos | Estructura |
|--------|---------------------|-------------|
| 0NF | Datos repetidos y no atómicos | Una sola tabla desorganizada |
| 1NF | Separación de valores múltiples | Registros atómicos |
| 2NF | Eliminación de dependencias parciales | Tablas por entidad (Curso, Profesor, Estudiante, Inscripción) |
| 3NF | Eliminación de dependencias transitivas | Facultad separada; modelo completamente normalizado |
