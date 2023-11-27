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
		FROM Producto p NATURAL JOIN Factura NATURAL JOIN Detalle NATURAL JOIN Cliente
		WHERE (Cliente.DNI = 45789456)
		EXPECT (
			SELECT p.nombre, p.descripcion, p.precio, p.stock
			FROM Producto p NATURAL JOIN Factura NATURAL JOIN Detalle NATURAL JOIN Cliente
			WHERE (Cliente.apellido = 'Garcia')
		)
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

### Ejercicio 3:
**Club** = (codigoClub, nombre, anioFundacion, codigoCiudad(FK))

**Ciudad** = (codigoCiudad, nombre)

**Estadio** = (codigoEstadio, codigoClub(FK), nombre, direccion)

**Jugador** = (DNI, nombre, apellido, edad, codigoCiudad(FK))

**ClubJugador** = (codigoClub, DNI, desde, hasta)

1. Reportar nombre y anioFundacion de aquellos clubes de la ciudad de La Plata que no poseen estadio.

2. Listar nombre de los clubes que no hayan tenido ni tengan jugadores de la ciudad de Berisso.

3. Mostrar DNI, nombre y apellido de aquellos jugadores que jugaron o juegan en el club Gimnasia y Esgrima La PLata.

4. Mostrar DNI, nombre y apellido de aquellos jugadores que tengan más de 29 años y hayan jugado o juegan en algún club de la ciudad de Córdoba.

5. Mostrar para cada club, nombre de club y la edad promedio de los jugadores que juegan actualmente en cada uno.

6. Listar para cada jugador: nombre, apellido, edad y cantidad de clubes diferentes en los que jugó. (incluido el actual).

7. Mostrar el nombre de los clubes que nunca hayan tenido jugadores de la ciudad de Mar del Plata.

8. Reportar el nombre y apellido de aquellos jugadores que hayan jugado en todos los clubes.

9. Agregar con codigoClub 1234 el club “Estrella de Berisso” que se fundó en 1921 y que pertenece a la ciudad de Berisso. Puede asumir que el codigoClub 1234 no existe en la tabla Club.


```sql

	
```

