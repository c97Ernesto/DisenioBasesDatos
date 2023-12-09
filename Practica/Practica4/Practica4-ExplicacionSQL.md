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


#### Los atributos utilizados en el SELECT de una consulta SQL pueden tener asociados operaciones válidas para sus dominios.
- Así, por ejemplo sería válida una consulta que devuelva el salario de un empleado menos un 10% de retención.

	```sql

		SELECT nombre_empleado, salario * 0.9
		FROM empleados
		WHERE ocupacion = “Gerente”

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

#### Funciones de Agregación:
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






	```sql



	```
