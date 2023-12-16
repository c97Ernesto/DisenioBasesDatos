<div>
<h1 align="center">Diseño de Bases de Datos</h1>
</div>

<div>
<h2 align="center">Práctica 4 - SQL</h2>
</div>

**NOTA: Dadas las siguientes relaciones, resolver utilizando SQL las consultas planteadas.**

### Ejercicio 1:

**Cliente** (<u>idCliente</u>, nombre, apellido, DNI, telefono, direccion)

**Factura** (<u>nroTicket</u>, total, fecha, hora, idCliente(Fk))

**Detalle** (<u>nroTicket, idProducto</u>, cantidad, preciounitario)

**Producto** (<u>idProducto</u>, descripcion, precio, nombreP, stock)


1. Listar datos personales de clientes cuyo apellido comience con el string ‘Pe’. Ordenar por DNI.

	```sql

		SELECT nombre, apellido, DNI, telefono, direccion
		FROM Cliente
		WHERE (apellido LIKE 'Pe%')
		ORDER BY DNI

	```

2. Listar nombre, apellido, DNI, teléfono y dirección de clientes que realizaron compras solamente durante 2017.

	```sql

		SELECT nombre, apellido, DNI, telefono, direccion
		FROM Cliente NATURAL JOIN Factura
		WHERE (Factura.fecha BETWEEN '1/1/2017' and '31/12/2017')
		EXCEPT 
		SELECT nombre, apellido, DNI, telefono, direccion
		FROM Cliente NATURAL JOIN Factura
		WHERE (Factura.fecha < '1/1/2017' and Factura.fecha > '31/12/2017'))

	```

3. Listar nombre, descripción, precio y stock de productos vendidos al cliente con DNI:45789456, pero que no fueron vendidos a clientes de apellido ‘Garcia’.

	```sql

		SELECT p.nombre, p.descripcion, p.precio, p.stock
		FROM Producto p 
		NATURAL JOIN Factura 
		NATURAL JOIN Detalle 
		NATURAL JOIN Cliente
		WHERE (Cliente.DNI = 45789456) 
		AND p.idProducto NOT IN (
			SELECT d.idProducto
			FROM Detalle d
			NATURAL JOIN Factura
			NATURAL JOIN Cliente
			WHERE Cliente.apellido = 'Garcia'
		);

	```
	
4. Listar nombre, descripción, precio y stock de productos no vendidos a clientes que tengan teléfono con característica: 221 (La característica está al comienzo del teléfono). Ordenar por nombre.

	```sql
		
		SELECT p.nombre, p.descripcion, p.precio, p.stock
		FROM Producto p
		WHERE p.idProducto NOT IN (
			SELECT p.idProducto
			FROM Detalle NATURAL JOIN Factura NATURAL JOIN Cliente
			WHERE (Cliente.telefono LIKE 221%)
		)
		ORDER BY p.nombre;

	```

5. Listar para cada producto: nombre, descripción, precio y cuantas veces fué vendido. Tenga en cuenta que puede no haberse vendido nunca el producto.

	```sql

		SELECT p.nombreP, p.descripcion, p.precio, SUM(d.cantidad) AS cant	
		FROM Producto p LEFT JOIN Detalle d ON (d.idProducto = p.idProducto)
		GROUP BY p.idProducto, p.nombreP, p.descripcion, p.precio

		-- En SQL, cuando se utilizan funciones agregadas como COUNT(), SUM(), AVG(), MIN(), MAX(), etc., junto con la cláusula GROUP BY, todas las columnas de la declaración "SELECT" que no estén agregadas deben incluirse en la cláusula "GROUP BY". Esto asegura que la función SUM(d.cantidad) calcule una suma separada para cada producto único.
		
	```	

6. Listar nombre, apellido, DNI, teléfono y dirección de clientes que compraron los productos con nombre ‘prod1’ y ‘prod2’ pero nunca compraron el producto con nombre ‘prod3’.

	```sql
			
		SELECT nombre, apellido, DNI, telefono, direccion
		FROM Cliente c 
		INNER JOIN Factura f ON (c.idCliente = f.idCliente) 
		INNER JOIN Detalle d ON (f.nroTicket = d.nroTicket) 
		INNER JOIN Producto p ON (d.idProducto = p.idProducto)
		WHERE p.nombreP IN ('prod1', 'prod2')
		AND c.idCliente NOT IN (
			SELECT idCliente
			FROM Cliente
			INNER JOIN Factura f ON (Cliente.idCliente = f.idCliente) 
			INNER JOIN Detalle d ON (f.nroTicket = d.nroTicket) 
			INNER JOIN Producto p ON (d.idProducto = p.idProducto)
			WHERE (p.nombre = 'prod3')
		);
			
	```


7. Listar nroTicket, total, fecha, hora y DNI del cliente, de aquellas facturas donde se haya comprado el producto ‘prod38’ o la factura tenga fecha de 2019.

	```sql
			
		SELECT f.nroTicket, f.total, f.fecha, f.hora, c.DNI
		FROM Factura f 
		INNER JOIN Cliente c ON f.idCliente = c.idCliente
		INNER JOIN Detalle d ON f.nroTicket = d.nroTicket
		INNER JOIN Producto p ON d.idProducto = p.idProducto
		WHERE (p.nombreP = 'prod38') OR (fecha BETWEEN "1/1/2019" AND "31/12/2019");

	```

8. Agregar un cliente con los siguientes datos: nombre:’Jorge Luis’, apellido:’Castor’, DNI:40578999, teléfono:221-4400789, dirección:’11 entre 500 y 501 nro:2587’ y el id de cliente: 500002. Se supone que el idCliente 500002 no existe.

	```sql
		
		INSERT INTO Cliente (idCliente, nombre, apellido, DNI, telefono, direccion) 
		VALUES (500002, 'Jorge Luis', 'Castor', 40578999, '221-4400789', ’11 entre 500 y 501 nro:2587’);

	```

9. Listar nroTicket, total, fecha, hora para las facturas del cliente  ́Jorge Pérez ́ donde no haya comprado el producto  ́Z ́.

	```sql

		SELECT f.nroTicket, f.total, f.fecha, f.hora
		FROM Factura f 
		INNER JOIN Cliente c ON f.idCliente = c.idCliente
		WHERE (c.nombre = 'Jorge') AND (c.apellido = 'Perez') 
		AND f.nroTicket NOT IN (
			SELECT nroTicket
			FROM Detalle 
			INNER JOIN Producto ON Detalle.idProducto = Producto.idProducto
			WHERE Producto.nombreP = 'Z'
		);
		
	```

10. Listar DNI, apellido y nombre de clientes donde el monto total comprado, teniendo en cuenta todas sus facturas, supere $10.000.000.

	```sql

		SELECT DNI, apellido, nombre
		FROM Cliente 
		INNER JOIN Factura f ON Cliente.idCliente = f.idCliente
		GROUP BY Cliente.idCliente
		HAVING SUM(f.total) > 10000000;
		-- La cláusula HAVING filtra los grupos (clientes) según una condición después de la agregación. En este caso, filtra aquellos grupos cuya suma total de los campos total de la tabla Factura sea mayor a 10,000,000.
			
	```
	
### Ejercicio 2

**Agencia** (<u>razon_social</u>, dirección, telef, e-mail)

**Ciudad** (<u>codigoPostal</u>, nombreCiudad, añoCreación)

**Cliente** (<u>DNI</u>, nombre, apellido, teléfono, dirección)

**Viaje** (<u>fecha, hora, dni</u>, cpOrigen(fk), cpDestino(fk), razon_social(fk), descripcion) `cpOrigen y cpDestino corresponden a la ciudades origen y destino del viaje`

1. Listar razón social, dirección y teléfono de agencias que realizaron viajes desde la ciudad de ‘La Plata’ (ciudad origen) y que el cliente tenga apellido ‘Roma’. Ordenar por razón social y luego por teléfono.

	```sql

	-- Versión 1
	
		SELECT a.razon_social, a.direccion, a.telefono
		FROM Agencia a 
		INNER JOIN Viaje v ON a.razon_social = v.razon_social
		INNER JOIN Ciudad ON v.cpOrigen = Ciudad.codigoPostal
		INNER JOIN Cliente ON v.DNI = Cliente.DNI
		WHERE (Ciudad.nombreCiudad = 'La Plata') AND (Cliente.apellido = 'Roma')
		ORDER BY a.razon_social, a.telefono
		
	```
	
	```sql

	-- Versión 2
	
		SELECT a.RAZON_SOCIAL, a.direccion, a.telef
		FROM VIAJE v 
	    INNER JOIN AGENCIA a ON (v.razon_social = a.razon_social)
    	INNER JOIN Cliente c ON (v.DNI = c.DNI)
		WHERE (c.apellido = "Roma") AND (v.RAZON_SOCIAL IN (
		    SELECT v.RAZON_SOCIAL
		    FROM VIAJE v 
		    INNER JOIN CIUDAD c ON(v.cpOrigen = c.CODIGOPOSTAL)
		    WHERE c.nombreCiudad = "La Plata"
		))
		ORDER BY a.RAZON_SOCIAL, a.telef
		
	```

2. Listar fecha, hora, datos personales del cliente, ciudad origen y destino de viajes realizados en enero de 2019 donde la descripción del viaje contenga el String ‘demorado’.

	```sql

		SELECT Viaje.fecha, Viaje.hora, cli.nombre, cli.apellido, cli.telefono, cli.direccion, Ciudad.cpOrigen, Ciudad.cpDestino
		FROM Viaje v 
		INNER JOIN Cliente cli ON v.DNI = cli.DNI
		INNER JOIN Ciudad cOrigen ON v.cpOrigen = cOrigen.codigoPostal
		INNER JOIN Ciudad cDestino ON v.cpDestino = cDestino.codigoPostal
		WHERE v.fecha BETWEEN '2019/1/1' AND '2019/12/1' AND v.descripcion LIKE '%demorado%'
		
		
	```

3. Reportar información de agencias que realizaron viajes durante 2019 o que tengan dirección de mail que termine con ‘@jmail.com’.

	```sql

		SELECT a.direccion, a.telef, a.e-mail
		FROM Agencia a
		INNER JOIN Viaje v ON a.razon_social = v.razon_social
		WHERE (v.fecha BETWEEN '2019/1/1' AND '2019/12/1') OR (a.e-mail LIKE '%@jmail%')
		
	```

4. Listar datos personales de clientes que viajaron solo con destino a la ciudad de ‘Coronel Brandsen’

	```sql
	
	-- Versión 1
		SELECT 	cli.nombre, cli.apellido, cli.telefono, cli.direccion
		FROM Cliente cli
		INNER JOIN Viaje v ON  cli.DNI = v.dni
		INNER JOIN Ciudad c ON v.cpDestino = c.codigoPostal
		WHERE c.nombreCiudad = 'Coronel Brandsen' AND cli.DNI NOT IN (
			SELECT v.dni
			FROM Viaje v
			INNER JOIN Ciudad c ON v.cpDestino = c.codigoPostal
			WHERE c.nombreCiudad <> 'Coronel Brandsen'
		)
		
	```
	
	```sql
	
	-- Versión 2
		SELECT cli.nombre, cli.apellido, cli.telefono, cli.direccion
		FROM Cliente cli
		INNER JOIN Viaje v ON  cli.DNI = v.dni
		INNER JOIN Ciudad c ON v.cpDestino = c.codigoPostal
		WHERE c.nombreCiudad = 'Coronel Brandsen'
		EXCEPT 
		SELECT cli.nombre, cli.apellido, cli.telefono, cli.direccion
		FROM Cliente cli
		INNER JOIN Viaje v ON  cli.DNI = v.dni
		INNER JOIN Ciudad c ON v.cpDestino = c.codigoPostal
		WHERE c.nombreCiudad <> 'Coronel Brandsen'
		
	```

5. Informar cantidad de viajes de la agencia con razón social ‘TAXI Y’ realizados a ‘Villa Elisa’.

	```sql

		SELECT COUNT(*) AS cantViajes
		FROM Agencia a
		INNER JOIN Viaje v ON a.razon_social = v.razon_social
		INNER JOIN Ciudad c ON v.cpDestino = c.codigoPostal
		WHERE a.razon_social = 'TAXI Y' AND c.nombreCiudad = 'Villa Elisa'
		
	```

6. Listar nombre, apellido, dirección y teléfono de clientes que viajaron con todas las agencias.

	```sql

		SELECT cli.nombre, cli.apellido, cli.telefono, cli.direccion
		FROM Cliente cli
		WHERE NOT EXISTS (
			SELECT a.razon_social
			FROM Agencia a
			WHERE NOT EXISTS (
				SELECT v.dni
				FROM Viaje v 
				WHERE cli.DNI = v.dni AND v.razon_social = a.razon_social
			)
		)
		
	```



7. Modificar el cliente con DNI: 38495444 actualizando el teléfono a: 221-4400897.

	```sql

				
		
	```

8. Listar razon_social, dirección y teléfono de la/s agencias que tengan mayor cantidad de viajes realizados.

	```sql

		SELECT a.RAZON_SOCIAL, a.direccion, a.telef
		FROM Agencia a 
		INNER JOIN Viaje v ON a.razon_social = v.razon_social
		GROUP BY a.razon_social, a.direccion, a.telef
		HAVING COUNT(*) >= ALL (
			SELECT COUNT(*)
			FROM Viaje v
			GROUP BY v.razon_social
		)
		
	```

9. Reportar nombre, apellido, dirección y teléfono de clientes con al menos 10 viajes.

	```sql

				
		
	```

10. Borrar al cliente con DNI 40325692.

	```sql

				
		
	```


### Ejercicio 3

**Club** (codigoClub, nombre, anioFundacion, codigoCiudad(FK))

**Ciudad** (codigoCiudad, nombre)

**Estadio** (codigoEstadio, codigoClub(FK), nombre, direccion)

**Jugador** (DNI, nombre, apellido, edad, codigoCiudad(FK))

**ClubJugador** (codigoClub, DNI, desde, hasta)

1. Reportar nombre y anioFundacion de aquellos clubes de la ciudad de La Plata que no poseen estadio.

	```sql
	
	-- Versión 1
		SELECT cl.nombre, cl.anioFundacion
		FROM Club cl
		LEFT JOIN Ciudad ci ON cl.codigoCiudad = ci.codigoCiudad
		LEFT JOIN Estadio e ON cl.codigoClub = e.codigoClub
		WHERE ci.nombre = 'La Plata' AND e.codigoEstadio IS NULL		
		
	```
	
	```sql
	
	-- Versión 2
		SELECT cl.nombre, cl.anioFundacion
		FROM Club cl
		INNER JOIN Ciudad ci ON cl.codigoCiudad = ci.codigoCiudad
		WHERE ci.nombre = 'La Plata' AND c.CodigoClub NOT IN (
			SELECT e.codigoClub
			FROM Estadio e
		)
		
	```

2. Listar nombre de los clubes que no hayan tenido ni tengan jugadores de la ciudad de Berisso.

	```sql

		SELECT cl.nombre
		FROM Club cl
		LEFT JOIN ClubJugador clj ON cl.codigoClub = clj.codigoClub
		WHERE clj.DNI NOT IN (
			SELECT j.DNI
			FROM Jugador j 
			INNER JOIN Ciudad ci ON j.codigoCiudad = ci.codigoCiudad
			WHERE ci.nombre = 'Berisso'
		)
		
	```

3. Mostrar DNI, nombre y apellido de aquellos jugadores que jugaron o juegan en el club Gimnasia y Esgrima La Plata.

	```sql
	
	-- Versión 1
	
		SELECT j.DNI, j.nombre, j.apellido
		FROM Jugador j
		INNER JOIN ClubJugador clj ON j.DNI = clj.DNI
		WHERE clj.codigoClub IN (
			SELECT cl.codigoClub
			FROM Club cl
			WHERE cl.nombre = 'Gimnasia y Esgrima La Plata'
		)
		
	```
	
	```sql
	
	-- Versión 2
	
		SELECT j.DNI, j.nombre, j.apellido
		FROM Jugador j
		INNER JOIN ClubJugador clj ON j.DNI = clj.DNI
		INNER JOIN Club cl ON clj.codigoClub = cl.codigoClub
		WHERE cl.nombre = 'Gimnasia y Esgrima La Plata'
				
	```

4. Mostrar DNI, nombre y apellido de aquellos jugadores que tengan más de 29 años y hayan jugado o juegan en algún club de la ciudad de Córdoba.

	```sql

	-- Versión 1
	
		SELECT j.DNI, j.nombre, j.apellido
		FROM Jugador j
		INNER JOIN ClubJugador clj ON j.DNI = clj.DNI
		-- NATURAL JOIN Club clj
		INNER JOIN Club cl ON clj.codigoClub = cl.codigoClub
		-- NATURAL JOIN Club cl
		INNER JOIN Ciudad ci ON cl.codigoCiudad = ci.codigoCiudad
		-- NATURAL JOIN Ciudad ci
		WHERE j.edad > 29 AND ci.nombre = 'Córdoba'
		
	```


	```sql

	-- Versión 2
	
		SELECT j.DNI, j.nombre, j.apellido
		FROM Jugador j
		WHERE j.edad > 29 AND j.DNI IN (
			SELECT clj.DNI
			FROM ClubJugador clj 
			INNER JOIN Club cl ON clj.codigoClub = cl.codigoClub
			INNER JOIN Ciudad ci ON cl.codigoCiudad = ci.codigoCiudad
			WHERE ci.nombre = 'Córdoba'
		)				
		
	```
	
	
	```sql
	
	-- Versión 3
	
		SELECT j.DNI, j.nombre, j.apellido
		FROM Jugador j
		WHERE j.edad > 29 AND j.DNI IN (
			SELECT clj.DNI
			FROM ClubJugador clj
			WHERE clj.codigoClub IN (
				SELECT cl.codigoClub
				FROM Club cl
				WHERE cl.codigoCiudad IN (
					SELECT ci.codigoCiudad
					FROM Ciudad ci
					WHERE ci.nombre = 'Córdoba'
				)
			)
		)
		
	```

5. Mostrar para cada club, nombre de club y la edad promedio de los jugadores que juegan actualmente en cada uno.

	```sql

		SELECT cl.nombre, AVG(j.edad) AS promedioEdad
		FROM Club cl
		INNER JOIN ClubJugador clj ON cl.codigoClub = clj.codigoClub
		INNER JOIN Jugador j ON clj.DNI = j.DNI
		WHERE clj.hasta IN NULL
		GROUP BY cl.nombre;
		
	```
	
	

6. Listar para cada jugador: nombre, apellido, edad y cantidad de clubes diferentes en los que jugó. (incluido el actual).

	```sql

		SELECT j.nombre, j.apellido, j.edad, COUNT(DISTINCT clj.codigoClub) AS cantClubes
		FROM Jujador j
		INNER JOIN ClubJugador clj ON j.DNI = clj.DNI
		GROUP BY j.DNI, j.nombre, j.apellido, j.edad;
		
	```

7. Mostrar el nombre de los clubes que nunca hayan tenido jugadores de la ciudad de Mar del Plata.

	```sql

		SELECT cl.nombre
		FROM Club cl
		INNER JOIN ClubJugador clj
		WHERE clj.DNI NOT IN (
			SELECT j.DNI
			FROM Jugador j
			INNER JOIN Ciudad ci ON j.codigoCiudad = ci.codigoCiudad
			WHERE ci.nombre 'Mar del Plata'
		)
		
	```
	
	```sql
		
		SELECT cl.nombre
		FROM Club cl
		WHERE cl.codigoClub NOT IN (
			SELECT clj.codigoClub
			FROM ClubJugador clj
			INNER JOIN Jugador j ON clj.DNI = j.DNI
			INNER JOIN Ciudad ci ON j.codigoCiudad = ci.codigoCiudad
			WHERE ci.nombre 'Mar del Plata'
		)
				
		
	```
	

8. Reportar el nombre y apellido de aquellos jugadores que hayan jugado en todos los clubes.

	```sql

		SELECT j.nombre, j.apellido
		FROM Jugador j
		WHERE NOT EXISTS (
			SELECT cl.codigoClub
			FROM Club cl
			WHERE NOT EXISTS (
				SELECT clj.codigoClub
				FROM ClubJugador clj
				WHERE clj.DNI = j.DNI AND clj.codigoClub = cl.codigoClub
			)
		)

		
	```

9. Agregar con codigoClub 1234 el club “Estrella de Berisso” que se fundó en 1921 y que pertenece a la ciudad de Berisso. Puede asumir que el codigoClub 1234 no existe en la tabla Club.

	```sql

		INSERT INTO Club (codigoClub, DNI, desde, hasta)
		VALUES (1234, 'Estrella de Berisso', 1921, (
				SELECT c.codigoCiudad
				FROM Ciudad ci
				WHERE ci.nombre = 'Berisso'
			)
		)
		
	```


### Ejercicio 4

**Persona** (DNI, apellido, nomrbe, fecha_nacimiento, estado_civil, genero)

**Alumno** (DNI, legajo, anio_ingreso)

**Profesor** (DNI, matricula, nro_expediente)

**Titulo** (Cod_Titulo, nombre, descripcion)

**Titulo-Profesor** (Cod_Titulo, DNI, fecha)

**Curso** (Cod_Curso, nombre, descripcion, fecha_creacion, duracion)

**Alumno-Curso** (DNI, Cod_Curso, anio, desempenio, calificacion)

**Profesor-Curso** (DNI, Cod_Curso, fecha_desde, fecha_hasta)

1. Listar DNI, legajo y apellido y nombre de todos los alumnos que tegan año ingreso inferior a 2014.

	```sql

		SELECT p.DNI, a.legajo, p.apellido, p.nombre
		FROM Persona p
		NATURAL JOIN Alumno a
		WHERE a.anio_ingreso < 2014
		
	```

2. Listar DNI, matricula, apellido y nombre de los profesores que dictan cursos que tengan más 100 horas de duración. Ordenar por DNI.

	```sql

	-- Versión 1
	
		SELECT pro.DNI, pro.matricula, per.apellido, per.nombre
		From Persona per
		INNER JOIN Profesor pro ON per.DNI = pro.DNI
		INNER JOIN Profesor-Curso proC ON pro.DNI = proC.DNI
		INNER JOIN Curso c ON proC.Cod_Curso = c.Cod_Curso
		WHERE c.duracion > 100
		ORDER BY pro.DNI;
		
	```
	
	```sql
	
	-- Versión 2

		SELECT pro.DNI, pro.matricula, per.apellido, per.nombre
		From Persona per
		WHERE per.DNI IN (
			SELECT pro.DNI
			FROM Profesor pro
			WHERE pro.DNI IN (
				SELECT proC.DNI
				FROM Profesor-Curso proC
				WHERE proC.Cod_Curso IN (
					SELECT c.Cod_Curso
					FROM Curso c
					WHERE c.duracion > 100
				)
			)
		)
		ORDER BY per.DNI
		
	```
	
	```sql
	
	-- Versión 3

		SELECT pro.DNI, pro.matricula, per.apellido, per.nombre
		FROM Profesor pro
		INNER JOIN Persona per ON pro.DNI = per.DNI
		INNER JOIN Profesor-Curso proC ON pro.DNI = proC.DNI
		WHERE proC.Cod_Curso IN (
			SELECT c.Cod_Curso
			FROM Curso c
			WHERE c.duracion > 100
		)
		ORDER BY pro.DNI

	```



3. Listar el DNI, Apellido, Nombre, Género y Fecha de nacimiento de los alumnos inscriptos al curso con nombre “Diseño de Bases de Datos” en 2019.

	```sql
	
	-- Versión 1

		SELECT per.DNI, per.apellido, per.nombre, per.genero, per.Fecha_Nacimiento
		FROM Persona per
		WHERE per.DNI IN (
			SELECT a.DNI
			FROM Alumno a
			WHERE a.DNI IN (
				SELECT ac.DNI
				FROM Alumno-Curso ac
				WHERE ac.anio = 2019 AND ac.Cod_Curso IN (
					SELECT c.Cod_Curso
					FROM Curso c
					WHERE c.nombre = 'Diseño de Bases de Datos'
				)
			)
		)
		
	```
	
		```sql
	
	-- Versión 1

		SELECT per.DNI, per.apellido, per.nombre, per.genero, per.Fecha_Nacimiento
		FROM Persona per
		INNER JOIN Alumno a ON per.DNI = a.DNI
		INNER JOIN Alumno-Curso ac ON a.DNI = ac.DNI
		INNER JOIN Curso c ON ac.Cod_Curso = c.Cod_Curso
		WHERE ac.anio = 2019 AND c.nombre = 'Diseño de Bases de Datos'
		
	```

4. Listar el DNI, Apellido, Nombre y Calificación de aquellos alumnos que obtuvieron una calificación superior a 9 en los cursos que dicta el profesor “Juan Garcia”. Dicho listado deberá estar ordenado por Apellido.

	```sql

			SELECT per.DNI, per.apellido, per.nombre, ac.calificacion
			FROM Persona perAlumno
			INNER JOIN Alumno a ON per.DNI = a.DNI
			INNER JOIN Alumno-Curso ac ON a.DNI = ac.DNI
			INNER JOIN Profesor-Curso pc ON ac.Cod_Curso = pc.Cod_Curso
			INNER JOIN Profesor pro ON pc.DNI = pro.DNI
			INNER JOIN Persona perProfesor ON pro.DNI = perProfesor.DNI
			WHERE ac.calificacion > 9 AND perProfesor.nombre = "Juan" AND perProfesor.apellido = "Garcia"
			ORDER BY per.apellido
		
	```

5. Listar el DNI, Apellido, Nombre y Matrícula de aquellos profesores que posean más de 3 títulos. Dicho listado deberá estar ordenado por Apellido y Nombre.

	```sql

		SELECT per.DNI, per.apellido, per.nombre, pro.matricula
		FROM Persona per
		INNER JOIN Profesor pro ON per.DNI = pro.DNI
		INNER JOIN Titulo-Profesor tp ON pro.DNI = tp.DNI
		GROUP BY per.DNI
		HAVING COUNT (tp.Cod_Titulo) > 3
		ORDER BY per.apellido, per.nombre
		
	```

6. Listar el DNI, Apellido, Nombre, Cantidad de horas y Promedio de horas que dicta cada profesor. La cantidad de horas se calcula como la suma de la duración de todos los cursos que dicta.

	```sql

		SELECT per.DNI, per.apellido, per.nombre, SUM(c.duracion) AS cantHoras, AVG(c.duracion) AS promHoras
		FROM Persona per
		INNER JOIN Profesor pro ON per.DNI = pro.DNI
		INNER JOIN Profesor-Curso pc ON pro.DNI = pc.DNI
		INNER JOIN Curso c ON pc.Cod_Curso = c.Cod_Curso
		GROUP BY per.DNI, per.apellido, per.nombre
		
	```

7. Listar Nombre, Descripción del curso que posea más alumnos inscriptos y del que posea menos alumnos inscriptos durante 2019.

	```sql

		-- Curso con menos alumnos inscriptos
		SELECT c.nombre, c.descripcion
		FROM Curso c
		INNER JOIN Alumno-Curso ac ON c.Cod_Curso = ac.Cod_Curso
		WHERE ac.anio = 2019
		GROUP BY c.Cod_Curso, c.nombre, c.descripcion
		HAVING COUNT(ac.DNI) <= ALL (
			SELECT COUNT(ac1.DNI)
			FROM Alumno-Curso ac1
			WHERE ac1.anio = 2019
			GROUP BY ac1.Cod_Curso
		)

		UNION

		-- Curso con más alumnos inscriptos
		SELECT c.nombre, c.descripcion
		FROM Curso c
		INNER JOIN Alumno-Curso ac ON c.Cod_Curso = ac.Cod_Curso
		WHERE ac.anio = 2019
		GROUP BY c.Cod_Curso, c.nombre, c.descripcion
		HAVING COUNT(ac.DNI) >= ALL (
			SELECT COUNT(ac2.DNI)
			FROM Alumno-Curso ac2
			WHERE ac2.anio = 2019
			GROUP BY ac2.Cod_Curso
		)

		
	```

8. Listar el DNI, Apellido, Nombre, Legajo de alumnos que realizaron cursos con nombre conteniendo el string ‘BD’ durante 2018 pero no realizaron ningún curso durante 2019.

	```sql
	
	-- Versión 1

		SELECT	per.DNI, per.apellido, per.nombre, a.legajo
		FROM Persona per
		INNER JOIN Alumno a ON per.DNI = a.DNI
		INNER JOIN Alumno-Curso ac ON a.DNI = ac.DNI
		INNER JOIN Curso c ON ac.Cod_Curso = c.Cod_Curso
		WHERE c.nombre LIKE '%BD%' AND ac.anio = 2018 AND a.DNI NOT IN (
			SELECT ac2.DNI
			FROM Alumo-Curso ac2
			WHERE ac2.anio = 2019
		)
		
	```
	
	```sql
	
	-- Versión 2

		SELECT	per.DNI, per.apellido, per.nombre, a.legajo
		FROM Persona per
		INNER JOIN Alumno a ON per.DNI = a.DNI
		INNER JOIN Alumno-Curso ac ON a.DNI = ac.DNI
		INNER JOIN Curso c ON ac.Cod_Curso = c.Cod_Curso
		WHERE c.nombre LIKE '%BD%' AND ac.anio = 2018
		EXCEPT
		SELECT per.DNI, per.apellido, per.nombre, a.legajo
		FROM Persona per
		INNER JOIN Alumno a ON per.DNI = a.DNI
		INNER JOIN Alumno-Curso ac ON a.DNI = ac.DNI
		WHERE ac.anio = 2019
		
	```


9. Agregar un profesor con los datos que prefiera y agregarle el título con código: 25.

	```sql

		INSERT INTO Persona (DNI, apellido, nombre, fecha_nacimiento, estado_civil, genero)
		VALUES (23444333, Polo, Marco, 2/2/1976, 'casado', 'Masculino')
		
		INSERT INTO Profesor (DNI, matricula, nro_expediente)
		VALUES (23444333, 3232, 32)
		
		INSERT INTO Titulo-Profesor (Cod_Titulo, DNI, fecha)
		VALUES (25, 23444333, "23/12/1998")
		
	```

10. Modificar el estado civil del alumno cuyo legajo es ‘2020/09’, el nuevo estado civil es divorciado.

	```sql

		UPDATE Persona p
		SET estado_civil = 'divorciado'
		WHERE p.DNI IN (
			SELECT a.DNI
			FROM Alumno a
			WHERE a.legajo = '2020/09'
		)
		
	```

11. Dar de baja el alumno con DNI 30568989. Realizar todas las bajas necesarias para no dejar el conjunto de relaciones en estado inconsistente.

	```sql

				
		
	```


### Ejercicio 5

**Localidad** (CodigoPostal, nombreL, descripcion, numHabitantes)

**Arbol** (nroArbol, especie, anios, calle, nro, codigoPostal(fk))

**Podador** (DNI, nombre, apellido, telefono, fnac, codigoPostalVive(fk))

**Poda** (codPoda,fecha, DNI(fk),nroArbol(fk))

1. Listar especie, años, calle, nro. y localidad de árboles podados por el podador ‘Juan Perez’ y por el podador ‘Jose Garcia’.

	```sql
	
	-- Versión 1

		SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
		FROM Arbol a 
		INNER JOIN Localidad l ON a.codigoPostal = l.CodigoPostal
	    INNER JOIN Poda ON a.nroArbol = Poda.nroArbol
	    INNER JOIN Podador p ON Poda.DNI = p.DNI
		WHERE p.nombre = "Juan" AND p.apellido = "Perez" AND a.nroArbol IN (
		    SELECT Poda.nroArbol
	    	FROM Poda 
    	    INNER JOIN Podador p ON (Poda.DNI = p.DNI)
		    WHERE p.nombre = "Jose" AND p.apellido = "Garcia"
    	)

	```
	
	
	```sql

	--Versión 2
	
		SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
		FROM Arbol a 
		INNER JOIN Localidad l ON a.codigoPostal = l.CodigoPostal
	    INNER JOIN Poda ON a.nroArbol = Poda.nroArbol
	    INNER JOIN Podador p ON Poda.DNI = p.DNI
		WHERE p.nombre = "Juan" AND p.apellido = "Perez"
		INTERSECT
		SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
		FROM Arbol a 
		INNER JOIN Localidad l ON a.codigoPostal = l.CodigoPostal
	    INNER JOIN Poda ON a.nroArbol = Poda.nroArbol
	    INNER JOIN Podador p ON Poda.DNI = p.DNI
		WHERE p.nombre = "Jose" AND p.apellido = "Garcia"
		
	```

2. Reportar DNI, nombre, apellido, fnac y localidad donde viven podadores que tengan podas durante 2018.

	```sql

		SELECT p.DNI, p.nombre, p.apellido, p.fnac, l.nombreL
		FROM Podador p
		INNER JOIN Localidad l ON p.codigoPostalVive = l.CodigoPostal
		INNER JOIN Poda ON p.DNI = Poda.DNI
		WHERE YEAR(Poda.fecha) = 2018
		-- WHERE Poda.fecha BETWEEN '1/1/2018' AND '31/12/2018'
		
	```

3. Listar especie, años, calle, nro y localidad de árboles que no fueron podados nunca.

	```sql

		SELECT a.especie, a.anios, a.calle, a.nro, l.nombreL
		FROM Arbol a
		INNER JOIN Lodalidad l ON a.codigoPostal = l.CodigoPostal
		WHERE NOT EXISTS (
			SELECT Poda.nroArbol
			FROM Poda
			WHERE a.nroArbol = Poda.nroArbol
		)
		
	```
	
	```sql

		SELECT a.especie, a.anios, a.calle, a.nro, l.nombreL
		FROM Arbol a
		INNER JOIN Lodalidad l ON a.codigoPostal = l.CodigoPostal
		WHERE a.nroArbol NOT IN (
			SELECT Poda.nroArbol
			FROM Poda
		)
		
	```
	
	```sql

		SELECT a.especie, a.años, a.calle, a.nro, l.nombreL
		FROM Arbol a 
		INNER JOIN Localidad l ON a.codigoPostal = l.CodigoPostal
	    LEFT JOIN Poda ON a.nroArbol = Poda.nroArbol
		WHERE Poda.nroArbol IS NULL
		
	```

4. Reportar especie, años,calle, nro y localidad de árboles que fueron podados durante 2017 y no fueron podados durante 2018.

	```sql

				
		
	```

5. Reportar DNI, nombre, apellido, fnac y localidad donde viven podadores con apellido terminado con el string ‘ata’ y que el podador tenga al menos una poda durante 2018. Ordenar por apellido y nombre.

	```sql

		SELECT
		
	```

6. Listar DNI, apellido, nombre, teléfono y fecha de nacimiento de podadores que solo podaron árboles de especie ‘Coníferas’.

	```sql

				
		
	```

7. Listar especie de árboles que se encuentren en la localidad de ‘La Plata’ y también en la localidad de ‘Salta’.

	```sql

				
		
	```

8. Eliminar el podador con DNI: 22234566.

	```sql

				
		
	```

9. Reportar nombre, descripción y cantidad de habitantes de localidades que tengan menos de 100 árboles.

	```sql

			SELECT
		
	```


### Ejercicio 6

**Tecnico** (codTec, nombre, especialidad) `técnicos`

**Repuesto** (codRep, nombre, stock, precio)  `repuestos`

**RepuestoReparacion** (nroReparac, codRep, cantidad, precio) `repuestos utilizados en reparaciones.`

**Reparacion** (nroReparac, codTec, precio_total, fecha) `reparaciones realizadas.`

1. Listar todos los repuestos, informando el nombre, stock y precio. Ordenar el
resultado por precio.

	```sql

		SELECT repu.nombre, repu.stock, repu.precio
		FROM Repuesto repu
		ORDER BY repu.precio
		
	```

2. Listar nombre, stock, precio de repuesto que participaron en reparaciones durante 2019 y además no participaron en reparaciones del técnico ‘José Gonzalez’.

	```sql

		SELECT repu.nombre, repu.stock, repu.precio
		FROM Repuesto repu
		INNER JOIN RepuestoReparacion rp ON repu.codRep = rr.codRep
		WHERE rr.nroReparac IN (
			SELECT repa.nroReparac
			FROM Reparacion repa
			WHERE YEAR(repa.fecha) = 2019 AND repa.codTec NOT IN (
				SELECT t.codTec
				FROM Tecnico t
				WHERE t.nombre = 'José Gonzalez'	
			)
		)
		
	```
	
	```sql

		SELECT repu.nombre, repu.stock, repu.precio
		FROM Repuesto repu
		INNER JOIN RepuestoReparacion rr ON repu.codRep = rr.codRep
		INNER JOIN Reparación repa ON rr.nroReparac = repa.nroReparac
		WHERE YEAR(repa.fecha) = 2019
		EXCEPT
		SELECT repu.nombre, repu.stock, repu.precio
		FROM Repuesto repu
		INNER JOIN RepuestoReparacion rr ON repu.codRep = rr.codRep
		INNER JOIN Reparación repa ON rr.nroReparac = repa.nroReparac
		INNER JOIN Tecnico t ON repa.codTec = t.codTec
		WHERE t.nombre = 'José Gonzalez'
		
	```

3. Listar el nombre, especialidad de técnicos que no participaron en ninguna
reparación. Ordenar por nombre ascendentemente.

	```sql

		SELECT t.nombre, t.especialidad
		FROM Tecnico t
		WHERE t.codTec NOT IN (
			SELECT repa.codTec
			FROM Reparacion
		)
		ORDER BY t.nombre
		
	```
	
	```sql

		SELECT t.nombre, t.especialidad
		FROM Tecnico t
		WHERE NOT EXISTS (
			SELECT repa.codTec
			FROM Reparacion repa
			WHERE t.codTec = repa.codTec
		)
		OREDER BY t.nombre
		
	```

4. Listar el nombre, especialidad de técnicos solo participaron en reparaciones durante 2018.

	```sql
	
	-- Versión 1

		SELECT t.nombre, t.especialidad
		FROM Tecnicos t
		WHERE t.codTec IN (
			SELECT repa.codTec
			FROM Reparacion repa
			WHERE YEAR(repa.fecha) = '2018'
		) AND t.codTec NOT IN (
			SELECT repa.codTec
			FROM Reparacion repa
			WHERE YEAR(repa.fecha) != '2018'
		)
		
	```
	
	```sql
	
	-- Versión 2

		SELECT t.nombre, t.especialidad
		FROM Tecnicos t
		WHERE t.codTec IN (
			SELECT repa.codTec
			FROM Reparacion repa
			WHERE YEAR(repa.fecha) = '2018'
		) AND t.codTec NOT IN (
			SELECT repa.codTec
			FROM Reparacion repa
			WHERE YEAR(repa.fecha) != '2018'
		)
		
	```

5. Listar para cada repuesto nombre, stock y cantidad de técnicos distintos que lo utilizaron. Si un repuesto no participó en alguna reparación igual debe aparecer en dicho listado.

	```sql

		SELECT repu.nombre, repu.stock, COUNT(repa.codTec) AS cantTecnicos
		FROM Repuesto repu
		LEFT JOIN RepuestoReparacion rr ON repu.codRep = rr.codRep
		LEFT JOIN Reparacion repa ON rr.nroReparac
		GROUP BY repu.codRep, repu.nombre, repu.stock
		
	```

6. Listar nombre y especialidad del técnico con mayor cantidad de reparaciones
realizadas y el técnico con menor cantidad de reparaciones.

	```sql

		SELECT t.nombre, t.especialidad
		FROM Tecnico t INNER JOIN Reparacion repa (t.codTec = repa.codTec)
		GROUP BY t.codTec, t.nombre, t.especialidad 
		HAVING COUNT(t.codTec) >= ALL(
			SELECT COUNT(repa.codTec)
			FROM Reparacion repa
			GROUP BY repa.codTec
		)
		
		SELECT t.nombre, t.especialidad
		FROM Tecnico t INNER JOIN Reparacion repa (t.codTec = repa.codTec)
		GROUP BY t.codTec, t.nombre, t.especialidad 
		HAVING COUNT(t.codTec) <= ALL(
			SELECT COUNT(repa.codTec)
			FROM Reparacion repa
			GROUP BY repa.codTec
		)
		
	```

7. Listar nombre, stock y precio de todos los repuestos con stock mayor a 0 y que
dicho repuesto no haya estado en reparaciones con precio_total superior a 10000.

	```sql

		SELECT repu.nombre, repu.stock, repu.precio
		FROM Repuesto repu
		WHERE repu.stock > 0 AND repu.codRep NOT IN (
			SELECT rr.codRep		
			FROM RepuestoReparacion rr
			INNER JOIN Reparacion repa ON rr.nroReparac = repa.nroReparac
			WHERE repa.precio_total > 10000
		)
		
	```

8. Proyectar precio, fecha y precio total de aquellas reparaciones donde se utilizó algún repuesto con precio en el momento de la reparación mayor a $1000 y menor a
$5000.

	```sql

		SELECT repa.precio, repa.fecha, repa.precio_total
		FROM Reparacion repa
		INNER JOIN RepuestoReparacion rr ON repa.nroReparac = rr.nroReparac
		WHERE rr.precio > 1000 AND rr.precio < 5000
		
	```

9. Listar nombre, stock y precio de repuestos que hayan sido utilizados en todas las reparaciones.

	```sql

		SELECT repu.nombre, repu.stock, repu.precio
		FROM Repuesto repu
		WHERE NOT EXIST (
			SELECT rr.codRep
			FROM RepuestoReparacion rr
			WHERE repu.codRep = rr.codRep AND NOT EXISTS (
				SELECT repa.nroReparac
				FROM Reparacion
				WHERE rr.nroReparac = repa.nroReparac AND rr.codRep = repu.codRep
			)		
		);
			
	```

10. Listar fecha, técnico y precio total de aquellas reparaciones que necesitaron al menos 10 repuestos distintos.

	```sql

		SELECT repa.fecha, t.nombre, repa.precio_total
		FROM Reparacion repa
		INNER JOIN RepuestoReparacion rr ON repa.nroReparac = rr.nroReparac
		INNER JOIN Tecnico t ON t.codTec = repa.codTec
		GROUP BY repa.nroReparac, repa.fecha, t.nombre, repa.precio_total
		HAVING COUNT(DISTINCT rr.codRep) > 10;

		
	```


### Ejercicio 7

**Banda** (codigoB, nombreBanda, genero_musical, anio_creacion)

**Integrante** (DNI, nombre, apellido, direccion, email, fecha_nacimiento, codigoB(fk))

**Escenario** (nroEscenario, nombre_escenario, ubicacion, cubierto, m2, descripcion)

**Recital** (fecha, hora, nroEscenario, codigoB(fk))

1. Listar DNI, nombre, apellido,dirección y email de integrantes nacidos entre 1980 y 1990 y hayan realizado algún recital durante 2018.

	```sql

		SELECT i.DNI, i.nombre, i.apellido, i.direccion, i.email
		FROM Integrante i
		WHERE YEAR(i.fecha_nacimiento) BETWEEN '1980' AND '1990' 
		AND i.codigoB IN (
			SELECT b.codigoB
			FROM Banda b
			INNER JOIN Recital r ON b.codigoB = r.codigoB
			WHERE YEAR(r.fecha) = 2018
		);
		
	```

2. Reportar nombre, género musical y año de creación de bandas que hayan realizado
recitales durante 2018, pero no hayan tocado durante 2017 .

	```sql
	
	--Versión 1

		SELECT b.nombre, b.genero, b.año_creacion
		FROM Banda b
		INNER JOIN Recital r ON b.codigoB = r.codigoB
		WHERE YEAR(r.fecha) = 2018 
		AND b.codigoB NOT IN (
			SELECT r.codigoB
			FROM Recital r
			WHERE YEAR(r.fecha) = 2017
		);
		
	```
	
	```sql
	
	--Versión 2
	
		SELECT b.nombreBanda, b.genero_musical, b.año_creacion
		FROM Banda b 
		INNER JOIN Recital r ON b.codigoB = r.codigoB
		WHERE r.fecha BETWEEN "01/01/2018" AND "31/12/2018"
		EXCEPT
		(
			SELECT b.nombreBanda, b.genero_musical, b.año_creacion
			FROM Banda b 
			INNER JOIN Recital r ON b.codigoB = r.codigoB
			WHERE r.fecha BETWEEN "01/01/2017" AND "31/12/2017"
		);

	```

3. Listar el cronograma de recitales del dia 04/12/2018. Se deberá listar: nombre de la banda que ejecutará el recital, fecha, hora, y el nombre y ubicación del escenario correspondiente.

	```sql

		SELECT b.nombre, r.fecha, r.hora, e.nombre, e.ubicacion
		FROM Recital r
		INNER JOIN Escenario e ON r.nroEscenario = e.nroEscenario
		INNER JOIN Banda b ON r.codigoB = b.codigoB
		WHERE r.fecha = '4/12/2018'
		
	```

4. Listar DNI, nombre, apellido,email de integrantes que hayan tocado en el escenario con nombre ‘Gustavo Cerati’ y en el escenario con nombre ‘Carlos Gardel’.

	```sql
	
	--Versión 1

		SELECT i.DNI, i.nombre, i.apellido, i.email
		FROM Integrante i
		INNER JOIN Recital r ON i.codigoB = r.codigoB
		INNER JOIN Escenario e ON r.nroEscenario = e.nroEscenario
		WHERE e.nombre_escenario = 'Gustavo Cerati' 
		AND i.codigoB IN (
			SELECT r.codigoB
			FROM Recital r
			INNER JOIN Escenario e ON r.nroEscenario = e.nroEscenario
			WHERE e.nombre_escenario = 'Carlos Gardel' 
		)
		
	```
	
	```sql

		SELECT i.DNI, i.nombre, i.apellido, i.email
		FROM Recital r 
		INNER JOIN Integrante i ON (r.codigoB = i.codigoB)
		INNER JOIN Escenario e ON (r.nroEscenario = e.nroEscenario)
		WHERE e.nombre_escenario = "Gustavo Cerati"
		INTERSECT
		(
			SELECT i.DNI, i.nombre, i.apellido, i.email
			FROM Recital r 
			INNER JOIN Integrante i ON (r.codigoB = i.codigoB)
			INNER JOIN Escenario e ON (r.nroEscenario = e.nroEscenario)
			WHERE e.nombre_escenario = "Carlos Gardel"
		)
				
		
	```

5. Reportar nombre, género musical y año de creación de bandas que tengan más de 8
integrantes.

	```sql

		SELECT b.nombreBanda, b.genero_musical, b.anio_creacion
		FROM Banda b
		INNER JOIN Integrante i ON b.codigoB = i.codigoB
		GROUP BY b.codigoB, b.nombreBanda, b.genero_musical, b.anio_creacion
		HAVING COUNT(i.DNI) > 8;
		
	```

6. Listar nombre de escenario, ubicación y descripción de escenarios que solo tuvieron recitales con género musical rock and roll. Ordenar por nombre de escenario.

	```sql

		SELECT e.nombre_escenario, e.ubicacion, e.descripcion
		FROM Escenario e
		INNER JOIN Recital r ON e.nroEscenario = r.nroEscenario
		INNER JOIN Banda b ON r.codigoB = b.codigoB
		WHERE b.genero_musical = 'Rock And Roll' AND e.nroEscenario NOT IN (
			SELECT r.nroEscenario
			FROM Recital r
			INNER JOIN Banda b ON r.codigoB = b.codigoB
			WHERE b.genero_musical <> 'Rock And Roll'
		)
		ORDER BY e.nombre_escenario
		
	```
	
	```sql

		SELECT e.nombre_escenario, e.ubicacion, e.descripcion
		FROM Escenario e 
		INNER JOIN Recital r ON e.nroEscenario = r.nroEscenario
		INNER JOIN Banda b ON r.codigoB = b.codigoB
		WHERE b.genero_musical = 'Rock And Roll'
		EXCEPT 
		(
			SELECT e.nombre_escenario, e.ubicacion, e.descripcion
			FROM Escenario e 
			INNER JOIN Recital r ON e.nroEscenario = r.nroEscenario
			INNER JOIN Banda b ON r.codigoB = b.codigoB
			WHERE b.genero_musical <> 'Rock And Roll'
		)
		ORDER BY e.nombre_escenario
		
	```

7. Listar nombre, género musical y año de creación de bandas que hayan realizado recitales en escenarios cubiertos durante 2018. `cubierto es true, false según corresponda.`

	```sql
	
		SELECT b.nombreBanda, b.genero_musical, b.anio_creacion
		FROM Banda b
		INNER JOIN Recital r ON b.codigoB = r.codigoB
		INNER JOIN Escenario e ON r.nroEscenario = e.nroEscenario
		WHERE YEAR(r.fecha) = 2018 AND e.cubierto = true;

	```

8. Reportar para cada escenario, nombre del escenario y cantidad de recitales durante 2018.

	```sql

		SELECT e.nombre, COUNT(*) AS cantRecitales
		FROM Escenario e
		INNER JOIN Recital r ON e.nroEscenario = r.nroEscenario
		WHERE YEAR(r.fecha = 2018)
		GROUP BY e.nroEscenario, e.nombre
		
	```

9. Modificar el nombre de la banda ‘Mempis la Blusera’ a: ‘Memphis la Blusera’.

	```sql

		UPDATE Banda b
		SET nombreBanda = 'Memphis la Blusera'
		WHERE b.nombreBanda = ‘Mempis la Blusera’;
		
	```


### Ejercicio 8

**Equipo** (<u>codigoE</u>, nombreE, descripcionE)

**Integrante** (<u>DNI</u>, nombre, apellido,ciudad,email, telefono,codigoE(fk))

**Laguna** (<u>nroLaguna</u>, nombreL, ubicación,extension, descripción)

**TorneoPesca** (<u>codTorneo</u>, fecha,hora,nroLaguna(fk), descripcion)

**Inscripcion** (<u>codTorneo,codigoE</u>,asistio, gano) `asistio y gano son true o false según corresponda`

1. Listar DNI, nombre, apellido y email de integrantes que sean de la ciudad ‘La Plata’ y estén inscriptos en torneos a disputarse durante 2019.

	```sql

	-- Versión 1:

		SELECT i.DNI, i.nombre, i. apellido, i.email
		FROM Integrante i
		WHERE i.ciudad = 'La Plata' AND i.codigoE IN (
			SELECT Inscripcion.codigoE
			FROM Inscripcion
			INNER JOIN TorneoPesca t ON Inscripcion.codTec = t.codTorneo
			WHERE YEAR(t.fecha) = 2019
		);
		
	```

	```sql
	
	--Versión 2:
	
		SELECT int.DNI, int.nombre, int.apellido int.ciudad, int.email
		FROM Intengrante int 
		INNER JOIN Inscripcion ins ON int.codigoE = ins.codigoE
		INNER JOIN TorneoPesca t ON ins.codTorneo = t.codTorneo
		WHERE i.ciudad = "La Plata" 
		AND tp.fecha BETWEEN "01/01/2019" AND "31/12/2019"
		
	```
	
	

2. Reportar nombre y descripción de equipos que solo se hayan inscripto en torneos de 2018.

	```sql

		SELECT e.nombreE, e.descripcionE
		FROM Equipo e
		INNER JOIN Inscripcion ins ON e.codigoE = ins.codigoE
		INNER JOIN TorneoPesca t ON ins.codTorneo = t.codTorneo
		WHERE YEAR(t.fecha) = 2018 
		AND e.codigoE NOT IN (
			SELECT ins.codigoE
			FROM Inscripcion ins
			INNER JOIN TorneoPesca t ON ins.codTec = t.codTorneo
			WHERE YEAR(t.fecha) <> 2018
		)
		
	```

3. Listar DNI, nombre, apellido,email y ciudad de integrantes que asistieron a torneos en la laguna con nombre ‘La Salada, Coronel Granada’ y su equipo no tenga inscripciones a torneos a disputarse en 2019.

	```sql

		SELECT i.DNI, i.nombre, i.apellido, i.email, i.ciudad
		FROM Integrante i
		INNER JOIN Inscripcion ins ON i.codigoE = ins.codigoE
		INNER JOIN TorneoPesca t ON ins.codTorneo = t.codTorneo
		INNER JOIN Laguna l ON t.nroLaguna = l.nroLaguna
		WHERE ins.asistio = true AND l.nombre = 'La Salada, Coronel Granada'
		AND i.codigoE NOT IN (
			SELECT ins.codigoE
			FROM Inscripcion ins
			INNER JOIN TorneoPesca t ON ins.codTorneo = t.codTorneo
			WHERE YEAR(t.fecha) = 2019
		);

	```

	```sql

		SELECT i.DNI, i.nombre, i.apellido, i.ciudad, i.email
		FROM Integrante i 
		INNER JOIN Inscripcion ins ON i.codigoE = ins.codigoE
		INNER JOIN TorneoPesca t ON ins.codTorneo = t.codTorneo
		INNER JOIN Laguna l ON t.nroLaguna = l.nroLaguna
		WHERE l.nombreL = 'La Salada, Coronel Granada' AND i.asistio = true
		EXCEPT
		(
			SELECT i.DNI, i.nombre, i.apellido, i.ciudad, i.email
			FROM Integrante i 
			INNER JOIN Inscripcion ins ON (i.codigoE = ins.codigoE)
			INNER JOIN TorneoPesca tp ON (ins.codTorneo = tp.codTorneo)
			WHERE tp.fecha BETWEEN '01/01/2019' AND '31/12/2019'
		)

		
	```

4. Reportar nombre, y descripción de equipos que tengan al menos 5 integrantes.Ordenar por nombre y descripción.

	```sql

		SELECT e.nombre, e.descripcion
		FROM Equipo e
		INNER JOIN Integrante i ON e.codigoE = i.codigoE
		GROUP BY e.nombre, e.descripcion
		HAVING 
		
	```

5. Reportar nombre, y descripción de equipos que tengan inscripciones en todas las lagunas.

	```sql

				
		
	```

6. Eliminar el equipo con código: 10000.

	```sql

				
		
	```

7. Listar nombreL, ubicación,extensión y descripción de lagunas que no tuvieron torneos.

	```sql

				
		
	```

8. Reportar nombre, y descripción de equipos que tengan inscripciones a torneos a disputarse durante 2019, pero no tienen inscripciones a torneos de 2018.

	```sql

				
		
	```

9. Listar DNI, nombre, apellido, ciudad y email de integrantes que ganaron algún torneo que se disputó en la laguna con nombre: ‘Laguna de Chascomús’.

	```sql

				
		
	```


### Ejercicio 9

**Proyecto** (codProyecto, nombrP,descripcion, fechaInicioP, fechaFinP, fechaFinEstimada,DNIResponsable, equipoBackend, equipoFrontend) `DNIResponsable corresponde a un empleado, equipoBackend y equipoFrontend corresponden a un equipo`

**Equipo** (codEquipo, nombreE, descripcionTecnologias,DNILider) `DNILider corresponde a un empleado`

**Empleado** (DNI,nombre, apellido, telefono, direccion, fechaIngreso)

**Empleado_Equipo** (codEquipo,DNI, fechaInicio, fechaFin,descripcionRol)

1. Listar nombre, descripción, fecha de inicio y fecha de fin de proyectos ya finalizados que no fueron terminados antes de la fecha de fin estimada.

	```sql

				
		
	```

2. Listar DNI, nombre, apellido, telefono, dirección y fecha de ingreso de empleados que no son, ni fueron responsables de proyectos. Ordenar por apellido y nombre.

	```sql

				
		
	```

3. Listar DNI, nombre, apellido, teléfono y dirección de líderes de equipo que tenga más de un equipo a cargo.

	```sql

				
		
	```

4. Listar DNI, nombre, apellido, teléfono y dirección de todos los empleados que trabajan en el proyecto con nombre ‘Proyecto X’. No es necesario informar responsable y líderes.

	```sql

				
		
	```

5. Listar nombre de equipo y datos personales de líderes de equipos que no tengan empleados asignados y trabajen con tecnología ‘Java’.

	```sql

				
		
	```

6. Modificar nombre, apellido y dirección del empleado con DNI: 40568965 con los datos que desee.

	```sql

				
		
	```

7. Listar DNI, nombre, apellido, teléfono y dirección de empleados que son responsables de proyectos pero no han sido líderes de equipo.

	```sql

				
		
	```

8. Listar nombre de equipo y descripción de tecnologías de equipos que hayan sido asignados como equipos frontend y backend.

	```sql

				
		
	```

9. Listar nombre, descripción, fecha de inicio, nombre y apellido de responsables de proyectos a finalizar durante 2019.

	```sql

				
		
	```


	
### Ejercicio 10

**Vehiculo** (patente, modelo, marca, peso, km)

**Camion** (patente, largo, max_toneladas, cant_ruedas, tiene_acoplado)

**Auto** (patente, es_electrico, tipo_motor)

**Service** (fecha, patente, km_service, observaciones, monto)

**Parte** (cod_parte, nombre, precio_parte)

**Service_Parte** = (fecha, patente, cod_parte, precio)

1. Listar todos los datos de aquellos camiones que tengan entre 4 y 8 ruedas, y que hayan realizado algún service en los últimos 365 días. Ordenar por patente, modelo y marca.

	```sql
	
	
	```

2. Listar los autos que hayan realizado el service “cambio de aceite” antes de los 13.000 km o hayan realizado el service “inspección general” que incluya la parte “filtro de combustible”.

	```sql
	
	SELECT 	
	
	```

3. Listar nombre y precio de todas las partes que aparezcan en más de 30 service que hayan salido (partes) más de $4.000.

	```sql
	
		SELECT p.nombre, p.precio_parte
		FROM Parte p
		WHERE p.cod_parte IN (
			SELECT sP.cod_parte
			FROM  Service_Parte sP 
			WHERE sP.precio > 4000
			GROUP BY sp.cod_parte
			HAVING COUNT(*) > 30
		)
	
	```

4. Dar de baja todos los camiones con más de 250.000 km.

	```sql
	
		DELETE FROM Service
		WHERE Service.patente IN (
			SELECT Camion.patente
			FROM Camion 
				INNER JOIN Vehiculo ON Camion.patente = Vehiculo.patente
			WHERE Vehiculo.km > 250000
		)
		
		DELETE FROM Service_Parte
		WHERE Service_Parte.patente IN (
			SELECT Camion.patente
			FROM Camion
				INNER JOIN Vehiculo ON Camion.patente = Vehiculo.patente
			WHERE Vehiculo.km > 250000
		)
		
		DELETE FROM Vehiculo
		WHERE Vehiculo.patente IN (
			SELECT Camion.patente
			FROM Camion
				INNER JOIN Vehiculo ON Camion.patente = Vehiculo.patente
			WHERE Vehiculo.km > 250000
		)
		
		DELETE FROM Camion
		WHERE Camion.patente IN (
			SELECT Camion.patente
			FROM Camion
				INNER JOIN Vehiculo ON Camion.patente = Vehiculo.patente
			WHERE Vehiculo.km > 250000
		)
		
			
	
	```

5. Listar el nombre y precio de aquellas partes que figuren en todos los service realizados en el corriente año.

	```sql
		
		SELECT p.nombre, p.precio_parte
		FROM Parte
		WHERE EXISTS (
			SELECT * 
			FROM Service_Parte
			WHERE Service_Parte.cod_parte = Parte.cod_parte AND (YEAR(s.fecha) = YEAR(CURRENT_DATE))
		)
	
	```

6. Listar todos los autos cuyo tipo de motor sea eléctrico. Mostrar información de patente, modelo , marca y peso.

	```sql
	
		SELECT v.modelo, v.marca, v.peso
		FROM Vehiculo v
		INNER JOIN Auto a ON v.patente = a.patente
		WHERE a.es_electrico = TRUE
	
	```

7. Dar de alta una parte, cuyo nombre sea “Aleron” y precio $&400.

	```sql
	
		INSERT INTO Parte (cod_parte, nombre, precio_parte)
		VALUES ('2112', 'Aleron', '6400')
	
	```

8. Dar de baja todos los services que se realizaron al auto con patente ‘AWA564’.

	```sql
		
		DELETE FROM Service_Parte sP
		WHERE sP.patente = 'AWA564'
		DELETE FROM Service s
		WHERE s.patente = 'AWA564'
	
	```

9. Listar todos los vehículos que hayan tenido services durante el 2018.

	```sql
	
		SELECT v.patente, v.modelo, v.marca, v.peso
		FROM Vehiculo v
		INNER JOIN Service s ON s.patente = v.patente
		WHERE s.fecha BETWEEN "01/01/2018" AND "31/12/2018"
	
	```

### Ejercicio 11

**Box** = (nroBox,m2, ubicación, capacidad, ocupacion) //ocupación es un numérico indicando cantidad de mascotas en el box actualmente, capacidad es una descripción.

**Mascota** = (codMascota,nombre, edad, raza, peso, telefonoContacto)

**Veterinario** = (matricula, CUIT, nombYAp, direccion, telefono)

**Supervision** = (codMascota,nroBox, fechaEntra, fechaSale?, matricula(fk), descripcionEstadia) //fechaSale tiene valor null si la mascota está actualmente en el box.

1. Listar para cada veterinario cantidad de supervisiones realizadas con fecha de salida (fechaSale) durante enero de 2020. Indicar matricula, CUIT, nombre y apellido, dirección, teléfono y cantidad de supervisiones.

	```sql
	
		SELECT v.matricula, v.CUIT, v.nombYAp, direccion, telefono, COUNT(s.codMascota) AS cantSupervisiones
		FROM Veterinario 
		INNER JOIN Supervision s ON v.matricula = s.matricula
		WHERE s.fechaSale BETWEEN '1/1/2020' AND '31/1/2020'
		
	```

2. Listar CUIT, matricula, nombre, apellido,dirección y teléfono de veterinarios que no tengan mascotas bajo supervisión actualmente.

	```sql
	
		SELECT v.CUIT, v.matricula, v.nombYAp, v.direccion, v.telefono
		FROM Veterinario v
		WHERE v.matricula IN (
			SELECT s.matricula 
			FROM Supervision s
			WHERE s.fechaSale IS NULL
		)
		
	```

3. Listar nombre, edad, raza, peso y teléfono de contacto de mascotas fueron atendidas por el veterinario ‘Oscar Lopez’. Ordenar por nombre y raza de manera ascendente.

	```sql
	
	
	
	```

4. Modificar nombre y apellido al veterinario con matricula: ‘MP 10000’, deberá llamarse: ‘Pablo Lopez’.

	```sql
	
		UPDATE Veterinario
		SET nombYAp = 'Pablo Lopez'
		WHERE matricula = 'MP 10000'
	
	```

5. Listar nombre, edad, raza, peso de mascotas que tengan supervisiones con el veterinario con matricula : ‘MP 1000’ y con el veterinario con matricula: ‘MN 4545’.

	```sql
	
		SELECT m.nombre, m.edad, m.raza
		FROM Mascota m 
		WHERE m.codMascota IN (
			SELECT s.codMascota
			FROM Supervision s
			INNER JOIN Veterinario v ON  s.matricula = v.matricula
			WHERE v.matricula = 'MP 1000'
		) 
		AND m.codMascota (
			SELECT s.codMascota
			FROM Supervision s
			INNER JOIN Veterinario v ON  s.matricula = v.matricula
			WHERE v.matriculo = 'MP 1000' 
		);
		
	
	```

6. Listar nroBox, m2, ubicación, capacidad y nombre de mascota para supervisiones con fecha de entrada (fechaEntra) durante 2020.

### Ejercicio 12

**Barberia** = (<u>codBarberia</u>, razon_social, direccion, telefono)

**Cliente** = (<u>nroCliente</u>, DNI, nombYAp, direccionC, fechaNacimiento, celular)

**Barbero** = (<u>codEmpleado</u>, DNIB, nombYApB, direccionB, telefonoContacto, mail)

**Atencion** = (<u>codEmpleado,Fecha,hora</u>,codBarberia(fk), nroCliente(fk),descTratamiento, valor)

1. Listar DNI, nombYAp, direccionC, fechaNacimiento y celular de clientes que no tengan atención durante 2020.

	```sql

		SELECT c.DNI, c.nombYAp, c.direccionC, c.fechaNacimiento, c.celular
		FROM Cliente c
		WHERE c.nroCliente NOT IN (
			SELECT a.nroCliente
			FROM Atencion a
			WHERE a.fecha BETWEEN '1/1/2020' AND '31/12/2020'
		);
		
	```	

2. Listar para cada barbero cantidad de atenciones que realizaron durante 2018. Listar DNIB, nombYApB, direccionB, telefonoContacto, mail y cantidad de atenciones.

	```sql

		SELECT b.DNIB, b.nombYApB, b.direccionB, b.telefonoContacto, b.mail, 	COUN(a.codEmpleado) AS cantAtenciones
		FROM Barbero b
		LEFT JOIN Atencion a ON b.codEmpleado = a.codEmpleado
		WHERE a.fecha BETWEEN '1/1/2018' AND '31/12/2018'
		GROUP BY b.codEmpleado
		
		-- Si utilizo INNER JOIN dejo afuera a aquellos barberos que no tuvieron ninguna atención.
		
	```

3. Listar razón social, dirección y teléfono de barberias que tengan atenciones para el cliente con DNI:22283566 . Ordenar por razón social y dirección ascendente.

	```sql
	
		SELECT b.razon_social, b.direccion, b.telefono
		FROM Barberia b 
		INNER JOIN Atencion a ON b.codBarberia = a.codBarberia
		INNER JOIN Cliente c ON a.nroCliente = c.nroCliente
		WHERE c.DNI = 22283566
		ORDER BY b.razon_social ASC, b.direccion ASC;
		
	```

4. Listar DNIB, nombYApB, direccionB, telefonoContacto y mail de barberos que tengan atenciones con valor superior a 5000.

	```sql 

		SELECT b.DNIB, b.nombYApB, b.direccionB, b.telefonoContacto, b.mail
		FROM Barbero b 
		INNER JOIN Atencion a ON b.codEmpleado = a.codEmpleado
		WHERE (a.valor > 5000)
		
	```

5. Listar DNI, nombYAp, direccionC, fechaNacimiento y celular de clientes que tengan atenciones en la barbería con razón social: ‘Corta barba’ y también se hayan atendido en la barbería con razón social: ‘Barberia Barbara’.

	```sql
	
	-- Version 1:
	
		SELECT c.DNI, c.nombYAp, c.direccionC, c.fechaNacimiento, c.celular
		FROM Cliente c
		WHERE nroCliente IN (
			SELECT a.nroCliente
			FROM Atencion a
			INNER JOIN Barberia b ON a.codBarberia = b.codBarberia
			WHERE razon_social = 'Corta Barba'
		) AND nroCliente IN (
			SELECT a.nroCliente
			FROM Atencion a
			INNER JOIN Barberia b ON a.codBarberia = b.codBarberia
			WHERE razon_social = 'Barberia Barbara'
		);
		
	```
	
	```sql

	-- Version 2
	
		SELECT c.DNI, c.nombYAp, c.direccionC, c.fechaNacimiento, c.celular
		FROM Cliente c 
		INNER JOIN Atencion a ON (c.nroCliente = a.nroCliente)
		INNER JOIN Barberia b ON (a.codBarberia = b.codBarberia)
		WHERE b.razon_social = "Corta barba"
		
		INTERSECT 
		
		SELECT c.DNI, c.nombYAp, c.direccionC, c.fechaNacimiento, c.celular
		FROM Cliente c 
		INNER JOIN Atencion a ON (c.nroCliente = a.nroCliente)
		INNER JOIN Barberia b ON (a.codBarberia = b.codBarberia)
		WHERE b.razon_social = "Barberia Barbara"
		
	```

6. Eliminar el cliente con DNI: 22222222.

	```sql

	
		
	```

### Ejercicio 13

**Club** (IdClub,nombreClub,ciudad)

**Complejo** (IdComplejo,nombreComplejo, IdClub(fk))

**Cancha** (IdCancha,nombreCancha,IdComplejo(fk))

**Entrenador** (IdEntrenador, nombreEntrenador,fechaNacimiento, direccion)

**Entrenamiento** (IdEntrenamiento, fecha, IdEntrenador(fk), IdCancha(fk))

1. Listar nombre, fecha nacimiento y dirección de entrenadores que hayan tenido entrenamientos durante 2020.

	```sql

		SELECT dt.nombre, dt.fechaNacimiento, dt.direccion
		FROM Entrenador dt
		INNER JOIN Entrenamiento e ON dt.IdEntrenador = e.IdEntrenador
		WHERE e.fecha BETWEEN '1/1/2020' AND '31/12/2020'
		
	```

2. Listar para cada cancha del complejo “Complejo 1” , la cantidad de entrenamientos que se realizaron durante el 2019. Informar nombre de la cancha y cantidad de entrenamientos.

	```sql
	
	-- Version 1

		SELECT can.nombre, COUNT(Entrenamiento.IdEntrenamiento) AS cantEntrenamientos
		FROM Cancha can
		INNER JOIN Complejo com ON can.IdComplejo = com.IdComplejo
		WHERE com.nombreComplejo = 'Complejo1' 
		AND can.IdCancha IN (
			SELECT can.IdCancha
			FROM Entrenamiento
			WHERE Entrenamiento.fecha BETWEEN '1/1/2019' AND '31/12/2019'
		)
		GROUP BY can.nombreCancha;-
		
	```
	
	```sql
	
	-- Version 2
		SELECT can.nombre, COUNT(e.IdEntrenamiento) as cantEntrenamientos
		FROM Cancha can
		INNER JOIN Complejo com ON can.IdComplejo = com.IdComplejo
		INNER JOIN Entrenamiento e ON can.IdCancha = e.IdCancha
		WHERE com.nombreComplejo = 'Complejo1' 
		AND e.fecha BETWEEN '1/1/2019' AND '31/12/2019'
		GROUP BY can.nombreCancha;
		
	```

3.  Listar los complejos donde haya realizado entrenamientos el entrenador “Jorge Gonzalez”. Informar nombre de complejo, ordenar el resultado de manera ascendente.

	```sql

				
		
	```

4.  Listar nombre , fecha de nacimiento y dirección de entrenadores que hayan entrenado en la cancha “Cancha 1” y en la Cancha “Cancha 2”.

	```sql

		SELECT dt.nombreEntrenador, dt.fechaNacimiento, dt.direccion
		FROM Entrenador dt 
		INNER JOIN Entrenamiento e ON dt.IdEntrenador = e.IdEntrenador
		INNER JOIN Cancha can ON e.IdCancha = can.IdCancha
		WHERE can.nombreCancha = 'Cancha1'
		INTERSECT (
			SELECT dt.nombreEntrenador, dt.fechaNacimiento, dt.direccion
			FROM Entrenador dt 
			INNER JOIN Entrenamiento e ON dt.IdEntrenador = e.IdEntrenador
			INNER JOIN Cancha can ON e.IdCancha = e.IdCancha
			WHERE can.nombreCancha = 'Cancha2'
		)
		
	```

5. Listar todos los clubes en los que entrena el entrenador “Marcos Perez”. Informar nombre del club y ciudad.

	```sql
	-- Version 1
	
		SELECT Club.nombreClub, Club.ciudad
		FROM Club
		INNER JOIN Comlejo com ON Club.IdClub = com.IdClub
		INNER JOIN Cancha can ON com.IdComplejo = can.IdComplejo
		INNER JOIN Entrenamiento e ON can.IdCancha = e.IdCancha
		INNER JOIN Entrenado dt ON e.IdEntrenador = dt.IdEntrenador
		WHERE (dt.nombreEntrenador = 'Marcos Perez')
		
	```
	
	```sql
	-- Version 2: mejorando el filtrado de los datos
	
		SELECT Club.nombreClub, Club.ciudad
		FROM Entrenador dt
		INNER JOIN Entrenamiento e ON dt.IdEntrenador = e.IdEntrenador
		INNER JOIN Cancha can ON e.IdCancha = can.IdCancha
		INNER JOIN Complejo com ON can.IdComplejo = com.IdComplejo
		INNER JOIN Club ON com.IdClub = Club.IdClub
		WHERE dt.nombreEntrenador = 'Marcos Perez';
		
	```

6. Eliminar los entrenamientos del entrenador ‘Juan Perez’.

	```sql

		DELETE FROM Entrenamiento 
		WHERE IdEntrenador IN (
			SELECT IdEntrenador
			FROM Entrenador
			WHERE nombreEntrenador = 'Juan Perez'
		);

		-- La cláusula DELETE no permite INNER JOIN directamente
		
	```

### Ejercicio 14

**Cine** (idCine, nombreC, direccion)

**Sala** (nroSala, nombreS, descripción, capacidad,idCine(fk))

**Pelicula** (idPeli, nombre, descripción, genero)

**Funcion** (nroFuncion, nroSala(fk), idPeli(fk), fecha, hora, ocupación)//ocupación indica cantidad de espectadores de la función.

1. Listar nombre, descripción y género de películas con funciones durante 2020.

	```sql
		
		SELECT p.nombre, p.descripcion, p.genero
		FROM Pelicula p
		INNER JOIN Funcion f ON p.idPeli = f.idPeli
		WHERE f.fecha BETWEEN '1/1/2020' AND '31/12/2020'
				
		
	```

2. Listar para cada Sala cantidad de espectadores que asistieron durante 2020. Indicar nombre de la sala, nombre del cine y total de espectadores.

	```sql

		SELECT s.nombreS, c.nombreC, SUM(f.opcupacion) as totalEspectadores
		FROM Sala s
		INNER JOIN Cine c ON s.idCine = c.idCine
		INNER JOIN Funcion f ON s.nroSala = f.nroSala
		WHERE f.fecha BETWEEN '1/1/2020' AND '31/12/2020'
		GROUP BY s.nroSala, s.nombreS, c.nombreC
	
		
	```

3. Listar nombre de cine y dirección para los cines que tienen o tuvieron función para la película ‘Relic’. Ordenar por nombre de Cine y dirección desc.

	```sql

		SELECT c.nombreC, c.direccion
		FROM Cine c
		INNER JOIN Sala s ON c.idCine = s.idCine
		INNER JOIN Funcion f ON s.nroSala = f.nroSala
		INNER JOIN Pelicula p ON f.idPeli = p.idPeli
		WHERE p.nombre = 'Relic'
		ORDER BY c.nombreC, c.direccion DESC

		
	```

4. Listar nombre ,descripción y género de películas que tienen función en la sala con nombre ‘Sala Lola Membrives’ o tienen función en el cine con nombre ‘Gran Rex’.


	```sql
	
	-- Solución 1:
	
		SELECT p.nombre, p.descripcion, p.genero
		FROM Pelicula p
		INNER JOIN Funcion f ON p.idPeli = f.idPeli
		INNER JOIN Sala s ON f.nroSala = s.nroSala
		INNER JOIN Cine c ON s.idCine = c.idCine
		WHERE (s.nombreS = 'Sala Lola Membrives') OR (c.nombreC = 'Gran Rex')
				
	```
	
	```sql
	
	-- Solución 2:
	
		SELECT p.nombre, p.descripcion, p.genero
		FROM Pelicula p
		WHERE p.idPeli IN (
		    SELECT f.idPeli
    		FROM Funcion f
    		WHERE f.nroSala IN (
    		    SELECT s.nroSala
    		    FROM Sala s
    		    WHERE s.nombreS = 'Sala Lola Membrives'
		    )
		    OR f.nroSala IN (
        		SELECT s.nroSala
		        FROM Sala s
        		INNER JOIN Cine c ON s.idCine = c.idCine
        		WHERE c.nombreC = 'Gran Rex'
    		)
		);
				
	```

5. Listar nombre del cine y dirección de cines que tengan salas con capacidad superior a los 300 espectadores.

	```sql

		SELECT Cine.nombreC, Cine.direccion
		FROM Cine 
		NATURAL JOIN Sala
		WHERE (Sala.capacidad > 300)
		
	```

6. Agregar un cine con nombre cine ‘Cine Ricardo Darin’, dirección: ‘calle 2 nro 1900, La Plata’ e idCine: 5000, asuma que no existe dicho id.


	```sql

		INSERT INTO Cine (idCine, nombreC, direccion)
		VALUES (5000, ‘Cine Ricardo Darin’, ‘calle 2 nro 1900, La Plata’ )
		
	```

