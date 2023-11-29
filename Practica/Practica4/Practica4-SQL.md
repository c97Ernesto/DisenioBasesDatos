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
### Ejercicio 10

**Vehiculo** = (patente, modelo, marca, peso, km)

**Camion** = (patente, largo, max_toneladas, cant_ruedas, tiene_acoplado)

**Auto** = (patente, es_electrico, tipo_motor)

**Service** = (fecha, patente, km_service, observaciones, monto)

**Parte** = (cod_parte, nombre, precio_parte)

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
		WHERE EXIST (
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
			Select s.codMascota
			FROM Supervision s
			INNER JOIN Veterinario v ON  s.matricula = v.matricula
			WHERE v.matricula = 'MP 1000'
		) 
		AND m.codMascota (
			Select s.codMascota
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

