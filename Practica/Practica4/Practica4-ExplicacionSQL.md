<div>
<h2 align="center">SQL</h2>
</div>

## Lenguaje de Definición de datos
El modelo de datos relacional utiliza el concepto de relación, tupla y atributo, en tanto en SQL los términos corresponden a tabla, fila y columna.

Para administrar un modelo físico de datos, SQL presenta tres claúsulas básicas: CREATE, DROP y ALTER. Los mismos se corresonden con crear, borrar o modificar el exquema existente.

## Lenguaje de Manipulación de datos


### Estructura Básica

La estrucutra básica de una consulta SQL tiene el siguiente formato

	```sql

		SELECT lista_de_atributos
		FROM lista_de_tablas
		WHERE predicado

	```
	
- Donde la `lista_de_atributos` indica los nombres de los atributos que serán presetnados en el resultado. 
	- Estos atributos deben estar contenidos en las tablas indicadas en la consulta. 
	- La sentencia `SELECT` equivale a la operación de proyección del AR (con la única diferencia de que no se eliminan automáticamente las tuplas repetidas)
	
- Además `lista_de_tablas` indica las tablas de la BD necesarias para resolver la consulta.
	- Donde la sentencia `FROM` equivalte al producto cartesiano.
	
- Por último, `predicado` indica que condición debe cumplir las tuplas de las tablas para estar en el resultado final de la consulta.
	- Donde la sentencia `WHERE` equivale a la selección.

Las siguientes consultas son equivalentes:

	```sql

		SELECT atr1, atr2, atr3
		FROM tabla1, tabla2
		WHERE atr4 = ‘valor’

	```

- **π**<sub>atr1, atr2, atr3</sub> (**σ**<sub>atr = 'valor'</sub> (tabla1 **𝑥** tabla2))

#### Operador `*`
Permite reemplazar la escritura literal de todos los atributos de las tablas involucradas.

	```sql

		SELECT *
		FROM alumnos

	```
	
#### Operadores `DISTINCT` y `ALL`.
SQL define implícitamente el operador `ALL` aplicado a los campos (atributos) del `SELECT`, para eliminar tuplas repetidas aplicamos el operador `DISTINCT`.

	```sql

		SELECT DISTINCT(nombre)
		FROM alumnos

	```

#### Operadores `=` `<` `>` `>=` `<=` `<>`

	```sql
	
	-- Es menor a:
		Select nombre
		From alumno
		Where (dni > 24564321)
		
	```
	
	```sql
	
	-- Menor o igual a:
		Select nombre
		From alumno
		Where (dni<=24564321)

	```

	```sql

	-- Entre el rango de 4 a 6:
		SELECT nombre
		FROM carreras
		WHERE duracion_años >= 4 AND duración_años <= 6

	```

#### Operador `BETWEEN`.
Funciona igual que el ejemplo anterior donde se incluyen los extermos

	```sql
	
		SELECT nombre
		FROM carreras
		WHERE duracion_años BETWEEN 4 and 6

	```

#### Operador `IS NULL`.
Verifica si un atributo contiene el valor de NULL, que es el que se encuentra almacenado por defecto si no se define otro.

	```sql
	
		SELECT nombre, apellido
		FROM alumno
		WHERE (nombre IS NOT NULL)

	```

#### Los atributos utilizados en el SELECT de una consulta SQL pueden tener asociados operaciones válidas para sus dominios.
- Así, por ejemplo sería válida una consulta que devuelva el salario de un empleado menos un 10% de retención.

	```sql

		SELECT nombre_empleado, salario * 0.9
		FROM empleados
		WHERE ocupacion = “Gerente”

	```

### Operadores de String

#### Operador `LIKE`.
Brinda gran potencia para aquellas consultas que requieren manejo de Strings. Se puede combinar con:

- `%`: representa cualquier cadena de caracteres, inclusive la cadena vacía.

	```sql
	
		SELECT nombre, apellido
		FROM alumno
		WHERE (apellido LIKE '%or%')

	```

- `_` (guión bajo): sustituye solo el caracter del lugar donde aparece.
	
	```sql

		SELECT nombre, apellido
		FROM alumno
		WHERE (nombre LIKE '_ _ _')

	```

#### Cláusula `ORDER BY`
Permite ordenaar las tuplas resultantes por el atributo indicado. Por defecto se ordenan las tuplas de menor a mayor, si se desea obtener el resultado de mayor a menor, hay que utilizar el operador DESC.

- Ej: Presentar los nombres de todas las materias y el año de
curso, para la carrera informática, ordenados por año de curso.

	
	```sql

		SELECT nombre, año_curso
		FROM materias, carreras
		WHERE carreras.nombre = ‘Informática’ AND materias.idcarrera = carreras.idcarrera
		ORDER BY año_curso

	```
	
	```sql

		SELECT nombre, año_curso
		FROM materias, carreras
		WHERE carreras.nombre=‘Informática’ AND materias.idcarrera = carreras.idcarrera
		ORDER BY año_curso DESC

	```
	
- También es posible indicar más de un criterio de ordenación:

	```sql
	
		SELECT nombre, año_curso
		FROM materias, carreras
		WHERE carreras.nombre = ‘Informática’ AND materias.idcarrera = carreras.idcarrera
		ORDER BY año_curso, nombre


	```

### Funciones de Agregación:
Son funciones que operan sobre un conjunto de tuplas de entrada y producen un único valor de salida. SQL define 5 funciones de agregación:

- AVG: retorna como resultado el promedio del atributo indicado para todas las tuplas del conjunto.

	```sql

		SELECT AVG(resultado)
		FROM inscripciones i, alumnos a
		WHERE a.nombre = “Montesanti” AND i.idalumno = a.idalumno

	```

- COUNT: retorna como resultado la cantidad de tuplas involucradas en el conjunto de entrada.

	```sql
	
	-- Informar cuantos alumnos aprobaron al menos un final durante 2008.
		SELECT COUNT ( DISTINCT idalumno )
		FROM inscripciones
		WHERE resultado > 3 AND año = 2008

	```

- MAX: retorna como resultado el valor más grande dentro del conjunto de tuplas para el atributo indicado.

	```sql
	
	-- Retornar la máxima nota de alguna materia durante 2008.
		SELECT MAX( resultado )
		FROM inscripciones
		WHERE año=2008

	```

- MIN: retorna como resultado el valor más pequeño dentro del conjunto de tuplas para el atributo indicado.

- SUM: retorna como resultado la suma del valor del atributo indicado para todas las tuplas del conjunto.


#### Funciones de Agrupación
- `GROUP BY`: Cuando se necesitan agrupar las tuplas de una consulta por algún criterio SQL prevé la cláusula GROUP BY.

	```sql
	
		SELECT a.nombre, AVG (i.resultado)
		FROM inscripciones i, alumnos a
		WHERE a.idalumno = i.idalumno
		GROUP BY a.nombre
		
	-- Se generan n subconjuntos de tuplas, cada uno por un nombre de alumno diferente. Luego, dentro de cada grupo se obtiene el promedio del atributo resultado

	```

- `HAVING`: Cuando en las consulta se necesitan obtener resultados para grupos que satisfagan una determinada condición, la cual actúa como filtro de grupos. Dentro de la misma es posible indicar una condición que debe cumplir el grupo para ser tenido en cuenta.

	```sql
	
	-- Presentar para cada carrera la cantidad de materias que la componen, solo mostrar en el resultado aquellas carreras con más de 20 materias.
		SELECT c.nombre, count ( * )
		FROM carreras c, materias m
		WHERE c.idcarrera = m.idmateria
		GROUP BY c.nombre
		HAVING count ( * ) > 20

	```



### Subconsultas
Consiste en ubicar una consulta SQL dentro de otra.


	```sql

		SELECT nombre
		FROM carreras
		WHERE duración_años = (
			SELECT MAX(duración_años)
			FROM carreras
		)
	-- La subconsulta generada retorna un único valor que se corresponde con la duración máxima para una carrera. Luego, se compara la duración de cada carrera contra la duración máxima. Aquella carrera en la que coincidan ambos valores será presentada en el resultado final.
	
	```	
	
#### Operador `IN`:
Comprueba si un elemento es parte o no de un conjunto.

	```sql

	-- Informar el nombre de los alumnos que están inscriptos en carreras de 5 años de duración.
		SELECT nombre
		FROM alumnos
		WHERE idcarrera IN ( 
			SELECT idcarrera
			FROM carreras
			WHERE duración_años = 5 
		)

	```

	```sql

	-- Mostrar las materias que no hayan sido aprobadas por ningún alumno.
		SELECT nombre
		FROM materias
		WHERE idmateria NOT IN (
			SELECT idmaterias
			FROM inscripciones
			WHERE resultado > 3 
		)

	```

#### Claúsula `EXIST`:
Se utiliza para comprobar si una subconsulta generó o no alguna tupla como respuesta. El resultado de la cláusula `EXIST` es verdadero si la subconsulta tiene al menos una tupla, y falso en caso contrario.

	```sql

	-- Mostrar el nombre de los alumnos que hayan aprobado algún final durante 2007.
		SELECT nombre
		FROM alumnos
		WHERE EXIST (
			SELECT *
			FROM inscripciones
			WHERE alumnos.idalumno = inscripciones.idalumno
			AND resultado > 3 
		)
	-- Para que la subconsulta tenga una tupla es necesario encontrar al menos una inscripción para ese alumno que resulte aprobada.

	```

	```sql

	-- Mostrar aquellos alumnos que no se inscribieron en materia alguna
		SELECT a.nombre
		FROM alumnos a
		WHERE NOT EXIST (
			SELECT *
			FROM inscripciones i
			WHERE a.idalumno = i.idalumno
		)

	```

### Productos Naturales
Se realizan en la cláusula `FROM` indicando las tablas involucradas en el producto y luego de la sentencia `ON` la condición que debe cumplirse.

#### INNER JOIN
Realiza el Producto Natural clásico, reuniendo las tuplas que tienen sentido

	```sql
	
	-- Presentar todos los alumnos y su localidad de procedencia.
		SELECT a.nombre, l.nombre
		FROM alumnos a
		INNER JOIN localidades l ON a.idlocalidad = l.idlocalidad

	```

#### LEFT JOIN
Si tenemos: `Tabla1 LEFT JOIN Tabla2`.

- En primera instancia se reúne cada tupla de la tabla 1 con su correspondiente tupla en la tabla2, similar al INNER JOIN. 
- Luego, si alguna tupla de la tabla 1 no se reúne con una tupla de la tabla 2, igualmente aparece en el resultado, con los atributos de la tabla 2 con valor nulo.

#### RIGHT JOIN
Genera, primero, un INNER JOIN para luego completar con las tuplas de la tabla 2 que no tienen correspondencia en la tabla.

- `Tabla2 RIGHT JOIN Tabla1` es equivalente a `Tabla1 LEFT JOIN Tabla2`

#### FULL JOIN
Genera el equivalente a un INNER JOIN, LEFT JOIN y RIGTH JOIN en conjunto. Es decir, reúne las tuplas con sentido, luego agrega las tuplas de las tabla 1 sin correspondencia en la tabla 2 y, por último, reúne las tuplas de tabla2 sin correspondencia en tabla1.

### Otras operaciones: Insertar, Borrar y Modificar.

### Cláusula `INSERT INTO`
Cláusula para agregar tuplas a una tabla

	```sql

		INSERT INTO alumno (dni, nombre, apellido) VALUES (2827893, ‘Raul’,'Perez');

	```

### Cláusula `DELETE FROM`
Claúsula para borrar una tupla o un conjunto de tuplas de una tabla

	```sql

		DELETE FROM alumnos
		WHERE idlocalidad = (
			SELECT idlocalidad			
			FROM localidades
			WHERE nombre = “City Bell”)

	```

- Debe notarse que nuevamente la integridad referencial tendrá especial énfasis sobre la cláusula DELETE. Esto es, solamente pueden ser borradas de la tabla aquellas tuplas que cumplan las condiciones de integridad referencial definidas.

### Cláusula `UPDATE ... SET`
Se utiliza para modificar el contenido de uno o varios atributos de una tabla.

	```sql

		UPDATE carreras
		SET duración_años = 5
		WHERE nombre = “Arquitectura”

	```

	```sql



	```
	
	
	
	
	
	
	
	
	
