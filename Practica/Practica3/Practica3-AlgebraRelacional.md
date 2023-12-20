<div>
<h1 align="center">Diseño de Bases de Datos</h1>
</div>

<div>
<h2 align="center">Práctica 3 - Álgebra Relacional</h2>
</div>

**NOTA: Dadas las siguientes relaciones, resolver utilizando Álgebra Relacional las consultas planteadas.**

### Ejercicio 1:

**Cliente** (<u>idCliente</u>, nombre, apellido, DNI, telefono, direccion)

**Factura** (<u>nroTicket</u>, total, fecha, hora, idCliente(Fk))

**Detalle** (<u>nroTicket, idProducto</u>, cantidad, preciounitario)

**Producto** (<u>idProducto</u>, descripcion, precio, nombreP, stock)


1. Listar nombre, apellido, DNI, teléfono y dirección de clientes con DNI superior a 22222222.

	- Solución: **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> (**σ**<sub>DNI > "22222222"</sub> (Cliente))

2. Listar nombre, apellido, DNI, teléfono y dirección de clientes con DNI superior a 22222222 y que tengan facturas cuyo total no supere los $100000. 

	- Solución 1: **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> (**σ** <sub>(DNI > "22222222") ^ (total < 100000)</sub> (Cliente **|𝑥|** Factura))
	
	- Solución 2: **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> ((**σ**<sub>total < 100000</sub> (Factura)) **|𝑥|** (**σ**<sub>DNI > 22222222</sub> (Cliente))

3. Listar nombre, apellido, DNI, teléfono y dirección de clientes que realizaron compras durante 2020.
	- Solución 1: **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> (**σ**<sub>(fecha >= "1/1/2020") ^ (fecha <= "31/12/2020")</sub> (Cliente **|𝑥|** Factura))
	
	- Solución 2: **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> ((Cliente) **|𝑥|** (**σ**<sub>(fecha >= "1/1/2020") ^ (fecha <= "31/12/2020")</sub> (Factura)))

4. Listar nombre, apellido, DNI, teléfono y dirección de clientes que no realizaron compras durante 2020.
	- Clientes con facturas fuera del año 2020: 
		- **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> (**σ**<sub>(fecha <= "1/1/2020") ^ (fecha >= "31/12/2020")</sub> (Cliente **|𝑥|** Factura))
	
	- Clientes que no tienen facturas en el año 2020 (correcta):
		- variable **⇐** **π**<sub>idCliente, nombre, apellido, DNI, telefono, direccion</sub> ((**σ**<sub>(fecha >= "1/1/2020") ^ (fecha <= "31/12/2020")</sub> Factura) **|𝑥|** (Cliente))
		- **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> (variable **−** Cliente)

5. Listar nombre, apellido, DNI, teléfono y dirección de clientes que solo tengan compras durante 2020.
	- Solución: 
		- compraAntes2020 **⇐** (**σ**<sub>fecha <= "1/1/2020"</sub> (Cliente **|𝑥|** Factura))
		- compraDespues2020 **⇐** (**σ**<sub>fecha >= "31/12/2020"</sub> (Cliente **|𝑥|** Factura))
		- noCompranEn2020 **⇐** **π**<sub>idCliente, nombre, apellido, DNI, telefono, direccion</sub> (compraAntes2020 **⋃** compraDespues2020)
		- **π**<sub>idCliente, nombre, apellido, DNI, telefono, direccion</sub> (noCompranEn2020 **−** Clientes)
		
		
6. Listar nombre, descripción, precio y stock de productos no vendidos.
	- Solución: 
		- productosVendidos **⇐** **π**<sub>idProducto, descripcion, precio, nombreP, stock</sub>(Producto **|𝑥|** Detalle)
		- **π**<sub>nombreP, descripcion, precio, stock</sub> (preductosVendidos - Producto)
	

7. Listar  nombre, apellido, DNI, teléfono y dirección de clientes que no compraron el producto con nombre ‘ProductoX’ durante 2020.
	- Solución:
		- clientesCompraronProductoX **⇐** **π**<sub>idCliente, nombre, apellido, DNI, telefono, direccion</sub>(Cliente **|𝑥|** (**σ**<sub>fecha >= "1/1/2020" ^ fecha <= "31/12/2020"</sub>Factura **|𝑥|** (**σ** <sub>nombreP = "ProductoX"</sub> Producto) **|𝑥|** Detalle))
		- **π**<sub>nombre, apellido, DNI, telefono, direccion</sub>(Cliente **−** clientesCompraronProductoX)
		
8. Listar  nombre, apellido, DNI, teléfono y dirección de clientes que compraron el producto con nombre ‘Producto A’’ y no compraron el producto con nombre ‘Producto B’.
	- Solución:
		- compraronProductoA **⇐** **π**<sub>idCliente, nombre, apellido, DNI, telefono, direccion</sub> (Cliente **|𝑥|** (Factura **|𝑥|** (Detalle **|𝑥|** (**σ**<sub>nombreP = "ProductoA"</sub> Producto))))
		- compraronProductoB **⇐** **π**<sub>idCliente, nombre, apellido, DNI, telefono, direccion</sub> (Cliente **|𝑥|** (Factura **|𝑥|** (Detalle **|𝑥|** (**σ**<sub>nombreP = "ProductoB"</sub> Producto))))
		- **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> (compraronProductoA **−** compraronProductoB)
	
9. Listar nroTicket, total, fecha, hora y DNI del cliente, de aquellas  facturas donde se haya comprado el producto ‘Producto C’.
	- Solución:
		- **π**<sub>nroTicket, total, fecha, hora, DNI</sub> (Cliente **|𝑥|** (Factura **|𝑥|** (Detalle **|𝑥|** (**σ**<sub>nombreP = "ProductoC"</sub> Producto))))

10. Agregar un producto con id de producto 1000, descripción “mi producto”, precio $10000, nombreP “producto Z” y stock 1000. Se supone que el idProducto 1000 no existe.
	- Solución:
		- Producto **⇐** Producto **⋃** {(1000, "mi producto", 10000, "producto z", 1000)}
		
### Ejercicio 2:

**Banda** (codigoB, nombreBanda, genero_musical, anio_creacion)

**Integrante** (DNI, nombre, apellido, direccion, email, fecha_nacimiento, codigoB(fk))

**Escenario** (nroEscenario, nombre_escenario, ubicacion, cubierto, m2, descripcion)

**Recital** (fecha, hora, nroEscenario, codigoB(fk))

1. Listar datos personales de integrantes con apellido ‘Garcia’ o fecha de nacimiento anterior a 2005 que toquen en bandas de Rock And Roll.

	- Solución:
		- **π**<sub>nombre, apellido, direccion, fecha_nacimiento</sub>(**σ**<sub>Banda.codigoB = Integrante.codigoB</sub>(**σ**<sub>(apellido = "Garcia") OR (fecha_nacimiento < 2005)</sub> (Integrante)) **𝑥** (**σ**<sub>genero_musical = 'Rock And Roll'</sub> (Banda)))		
	

2. Listar nombre de escenario, ubicación y descripción de escenarios que no tuvieron recitales durante 2019.

	- Solución:
		- escenarios **⇐** **π**<sub>nroEscenario, nombre_escenario, ubicacion, descripcion</sub>(Escenario)
		- **π**<sub>nombre_escenario, ubicacion, descripcion</sub>(escenarios **−** **π**<sub>nroEscenario, nombre_escenario, ubicacion, descripcion</sub>(Escenario **|𝑥|** (**σ**<sub>fecha = 2019</sub>(Recital)))

		

3. Listar nombre de escenario, ubicación y descripción de escenarios que tuvieron recitales con género musical Rock And Roll o tuvieron recitales durante 2020.

	- Solución:
		- recitalRock **⇐** **π**<sub>nroEscenario, nombre_escenario, ubicacion, descripcion</sub>(Escenario **|𝑥|** Recital **|𝑥|** (**σ**<sub>genero_musical = 'Rock And Roll'</sub>(Banda))
		- recital2020 **⇐** **π**<sub>nroEscenario, nombre_escenario, ubicacion, descripcion</sub>(Escenario **|𝑥|** (**σ**<sub>(fecha >= '1/1/2020') ^ (fecha <= '31/12/2020')</sub>(Recital))
		- **π**<sub>nroEscenario, nombre_escenario, ubicacion, descripcion</sub> (recitalRock **⋃** recital2020)
		

4. Listar nombre, género musical y año de creación de bandas que hayan realizado recitales en escenarios cubiertos durante 2019. `cubierto es true, false según corresponda.`

	- Solución:
		- **π**<sub>nombre, genero_musical, anio_creacion</sub> (Banda **|𝑥|** (**σ**<sub>(fecha >= '1/1/2019') ^ (fecha <= '31/12/2019')</sub>  (Recital)) **|𝑥|** (**σ**<sub>cubierto = true</sub> (Escenario))))

5. Listar DNI, nombre, apellido,dirección y email de integrantes nacidos entre 2000 y 2005 y que toquen en bandas con género pop que hayan tenido recitales durante 2020.

	- Solución:
		- nacidosEntre **⇐** **π**<sub>DNI, nombre, apellido, direccion, email, codigoB</sub> (**σ**<sub>(fecha_nacimiento >= '1/1/2000') ^ (fecha <= '31/12/2005')</sub> (Integrante))
		- bandasPop **⇐** **π**<sub>codigoB</sub>((**σ**<sub>(fecha >= '1/1/2020') ^ (fecha <= '31/12/2020')</sub> (Recital)) **|𝑥|** (**σ**<sub>genero_musical = 'Pop'</sub> (Banda)))
		- nacidosEntreConBandasPop **⇐** **π**<sub>DNI, nombre, apellido, direccion, email</sub> (nacidosEntre **|𝑥|** bandasPop)
		

6. Listar DNI, nombre, apellido,email de integrantes que hayan tocado en el escenario con nombre ‘Gustavo Cerati’ y no hayan tocado en el escenario  con nombre ‘Carlos Gardel’.
	
	- Solución 1:
	
		- Cerati **⇐** (Integrante **|𝑥|** Recital **|𝑥|** (**σ**<sub>nombre_escenario = 'Gustavo Cerati'</sub>(Escenario))
		- Gardel **⇐** (Integrante **|𝑥|** Recital **|𝑥|** (**σ**<sub>nombre_escenario = 'Carlos Gardel'</sub>(Escenario))
		- **π**<sub>DNI, nombre, apellido, email</sub>(Cerati **−** Gardel)
	

7. Modificar el año de creación de la banda de nombre ‘Ratones Paranoicos’ a: 1983.

	- Solución:
		- **σ**<sub>anio_creacion **⇐** '1983'</sub>(**σ**<sub>nombreBanda = 'Ratones Paranoicos'</sub> (Banda))

8. Reportar  nombre, género musical y año de creación de bandas que hayan realizado recitales durante 2019, y además hayan tocado durante 2020.

	- Solución:
		- recital2019 **⇐** (Banda **|𝑥|** (**σ**<sub>(fecha >= '1/1/2019') ^ (fecha <= '31/12/2019')</sub> (Recital)))
		 
		- tocaron2020 **⇐** (Banda **|𝑥|** (**σ**<sub>(fecha >= '1/1/2020') ^ (fecha <= '31/12/2020')</sub> (Recital)))
		
		- **π**<sub>nombre, genero_musical, anio_creacion</sub> (rectial2019 **⋂** tocaron2020)

9. Listar el cronograma de recitales del dia 04/12/2019. Se deberá listar: nombre de la banda que ejecutará el recital, fecha, hora, y el nombre y ubicación del escenario correspondiente.

	- Solución:
		- **π**<sub>Banda.nombre, Recital.fecha, Recital.hora, Escenario.nombre_escenario, Escenario.ubicacion</sub> (Banda **|𝑥|** (**σ**<sub>(fecha <= '4/12/2019')</sub> (Recital **|𝑥|** Escenario))


### Ejercicio 3:

**Agencia** (RAZON_SOCIAL, dirección, telef, e-mail)

**Ciudad** (CODIGOPOSTAL, nombreCiudad, añoCreación)

**Cliente** (DNI, nombre, apellido, teléfono, dirección)

**Viaje** (FECHA, HORA, DNI, cpOrigen (Fk), cpDestino (Fk), razon_social(Fk), descripcion) //cpOrigen y cpDestino corresponden a la ciudades origen y destino del viaje.

1. Eliminar el cliente con DNI:25326992.
	- Solución 1 (la base queda inconsistente por no borrar los viajes del cliente): 
		- Cliente **⇐** Cliente **−** **σ**<sub>DNI = "25326992"</sub> (Cliente)
		
	- Solución 2 (borrado en cascada para mantener la integridad referencial de la base de datos): 
		- clienteAEliminar **⇐** **σ**<sub>DNI = "25326992"</sub> (Cliente)
		- viajesDelCliente **⇐** (Viaje **|𝑥|** clienteAEliminar)
		- Viaje **⇐** (Viaje **−** viajesDelCliente)
		- Cliente **⇐** (Cliente **−** clienteAEliminar)

2. Listar datos personales de clientes que solo realizaron viajes locales.(En cada viaje realizado coincide la localidad origen con la destino, cpOrigen y cpDestino).
	- Solución: **π** <sub>nombre, apellido, telefono, direccion</sub> (Cliente **|𝑥|** (**σ**<sub>cpOrigen = cpDestino</sub> Viaje))

3. Listar información de agencias que no tengan viajes para el cliente DNI:22222222 durante el primer semestre de 2020.
	
	- Solución:
		- agenciasQueTienen **⇐** **π**<sub>RAZON_SOCIAL, direccion, telef, e-mail</sub> (Agencia **|𝑥|** (**σ**<sub>DNI = "22222222"</sub> Cliente **|𝑥|** (**σ**<sub>((FECHA > "1/1/2020") ^ (FECHA < "1/7/2020")</sub> Viaje)))
		- **π**<sub>direccion, telef, e-mail</sub> (Agencia - agenciasQueTienen)
	

4. Listar información de agencias que realizaron viajes durante 2019 y no realizaron viajes durante 2020.
	- viajesEn2019 **⇐** **π**<sub>RAZON_SOCIAL, direccion, telef, e-mail</sub> (Agencia **|𝑥|** (**σ**<sub>((FECHA >= "1/1/2019") ^ (FECHA <= "31/12/2019")</sub> Viaje)
	- viajesEn2020 **⇐** **π**<sub>RAZON_SOCIAL, direccion, telef, e-mail</sub> (Agencia **|𝑥|** (**σ**<sub>((FECHA >= "1/1/2020") ^ (FECHA <= "31/12/2020")</sub> Viaje)
	- **π**<sub>direccion, telef, e-mail</sub> (viajesEn2019 **−** viajesEn2020)

5. Agregar una agencia de viajes con los datos que desee.
	- Agencia **⇐** Agencia **⋃** {("Turismo", "Julio A.Roca", "0303456", "agencia1@gmail.com")}

6. Listar datos personales de clientes que viajaron con ciudad destino ‘Lincoln’ pero no realizaron viajes con origen ‘La Plata’.
	- cpLinconln **⇐** **π**<sub>CODIGOPOSTAL</sub> (**σ**<sub>nombreCiudad = "Lincoln"</sub> (Ciudad))
	- cpLaPlata **⇐** **π**<sub>CODIGOPOSTAL</sub> (**σ**<sub>nombreCiudad = "La Plata"</sub> (Ciudad))
	- viajaronLincoln **⇐** **π**<sub>DNI, nombre, apellido, telefono, direccion</sub> ((Cliente) **|𝑥|** (**σ**<sub>cpDestino = cpLinconln</sub> (Viaje))
	- viajaronLaPlata **⇐** **π**<sub>DNI, nombre, apellido, telefono, direccion</sub> ((Cliente) **|𝑥|** (**σ**<sub>cpOrigen = cpLaPlata</sub> (Viaje))
	- **π**<sub>DNI, nombre, apellido</sub> (viajaronLincoln **−** viajaronLaPlata)
	

7. Listar nombre, apellido, dirección y teléfono de clientes que viajaron con todas las agencias.
	- **π**<sub>nombre, apellido, direccion y telef</sub> (Clientes **|𝑥|** Viaje **|𝑥|** Agencia) **π**<sub>RAZON_SOCIAL</sub> (Agencia)

8. Listar código postal, nombre Ciudad y año creación de ciudades que no recibieron viajes durante 2020.
	- cpsDestino2020 **⇐** **π**<sub>cpDestino</sub>(**σ**<sub>(FECHA > "1/1/2020") ^ (FECHA < "31/12/2020")</sub> (Viaje))
	- ciudadesDestinoEn2020 **⇐** **π**<sub>CODIGOPOSTAL, nombreCiudad, añoCreación</sub>(Ciudad **|𝑥|** cpsDestino2020)
	- **π**<sub>CODIGOPOSTAL, nombreCiudad, añoCreación</sub> (Ciudad **−** ciudadesDestinoEn2020
	
9. Reportar información de agencias que realizaron viajes durante 2019 o que tengan dirección igual a  ‘General Pinto’.

	- viajesEn2019 **⇐** **π**<sub>RAZON_SOCIAL, direccion, telef, e-mail</sub> (Agencia **|𝑥|** (**σ**<sub>((FECHA >= "1/1/2019") ^ (FECHA <= "31/12/2019")</sub> Viaje)
	- dirGeneralPinto **⇐** **π**<sub>RAZON_SOCIAL, direccion, telef, e-mail</sub> (Agencia **|𝑥|** Viaje **|𝑥|** (**σ**<sub>nombreCiudad = 'General Pinto'</sub>(Ciudad)))
	- **π**<sub>RAZON_SOCIAL, direccion, telef, e-mail</sub>(viajesEn2019 **⋃** dirGeneralPinto)
	
10. Actualizar el teléfono del cliente con DNI: 2789655 a: 221-4400345.

		- **σ**<sub>telefono **⇐** '221-4400345'</sub>(**σ**<sub>telefono = '221-4400345'</sub> (Cliente))
	

### Ejercicio 4:

**Equipo** (<u>codigoE</u>, nombreE, descripcionE)

**Integrante** (<u>DNI</u>, nombre, apellido, ciudad, email, telefono, codigoE)

**Laguna** (<u>nroLaguna</u>, nombreL, ubicacion, extension, descripcion)

**TorneoPesca** (<u>codTorneo</u>, fecha, hora, nroLaguna, descripcion)

**Inscripcion** (<u>codTorneo, codigoE</u>,asistio, gano) `asistio y gano son true o false según corresponda`

1. Listar DNI, nombre, apellido y email de integrantes que sean de la ciudad ‘La Plata’ y estén inscriptos en torneos que se disputaron durante 2019.

2. Reportar  nombre y descripción de equipos que solo se hayan inscripto en torneos de 2019.

3. Listar nombre, ubicación, extensión y descripción de lagunas que hayan tenido torneos durante 2019 y no hayan tenido torneos durante 2020.

4. Listar para la laguna  con nombre ‘laguna x’, nombre y descripción de equipos ganadores de torneos que se disputaron durante 2019 en la mencionada laguna.

	- Solución:
		- ganadores **⇐** (**σ**<sub>gano = true</sub> (Inscripcion))
		- torneos2019 **⇐** (**σ**<sub>((fecha > "1/1/2019") ^ (FECHA < "31/12/2019")</sub> TorneoPesca))
		- lagunaX **⇐** (**σ**<sub>nombreL = 'laguna x'</sub> (Laguna))
		- **π**<sub>nombreE, descripcionE</sub> (Equipo **|𝑥|** ganadores **|𝑥|** torneos2019 **|𝑥|** lagunaX)
		

5. Reportar  nombre, y descripción de equipos que tengan inscripciones en todas las lagunas.

	- Solución: 
		- lagunas **⇐** **π**<sub>nroLaguna</sub> (Laguna)
		- **π**<sub>nombreE, descripcionE</sub> (Equipo **|𝑥|** Inscripcion **|𝑥|** Laguna) **%** lagunas)

6. Eliminar el equipo con código: 10000.

	- Solución
		- Integrante **⇐** Integrante **−** (**σ**<sub>codigoE = 10000</sub>(Integrante))
		- Inscripcion **⇐** Inscripcion **−** (**σ**<sub>codigoE = 10000</sub>(Inscripcion))
		- Equipo **⇐** Equipo **−** (**σ**<sub>codigoE = 10000</sub>(Equipo))
		

7. Listar nombreL, ubicación,extensión y descripción de lagunas que no tuvieron torneos. 
	
	- Solución:
		- lagunasConTorneo **⇐** **π**<sub>nroLaguna, nombreL, ubicacion, extension, descripcion</sub>(Laguna **|𝑥|** TorneoPesca)
		
		- **π**<sub>nroLaguna, nombreL, ubicacion, extension, descripcion</sub>(Laguna **−** lagunasConTorneo)
			

8. Reportar  nombre, y descripción de equipos que tengan inscripciones a torneos a disputarse durante 2019, pero no tienen inscripciones a torneos de 2020.

9. Listar DNI, nombre, apellido, ciudad y email de integrantes que asistieron o ganaron algún torneo que se disputó en la laguna con nombre: ‘Laguna Brava’.


### Ejercicio 5:
**Club** (codigoClub, nombre, anioFundacion, codigoCiudad(FK))

**Ciudad** (codigoCiudad, nombre)

**Estadio** (codigoEstadio, codigoClub(FK), nombre, direccion)

**Jugador** (dni, nombre, apellido, edad, codigoCiudad(FK))

**ClubJugador** (codigoClub, dni, desde, hasta)

1. Reportar nombre y año de fundación de clubes de la ciudad de La Plata, además del nombre y dirección del estadio del mismo.

	- codLaPlata **⇐** **π**<sub>codigoCiudad</sub> (**σ**<sub>codigoCiudad = "La Plata"</sub> (Ciudad))
	- infoClub **⇐** **π**<sub>codigoClub, nombre, anioFundacion</sub> (**σ**<sub>codigoCiudad = codLaPlata</sub> (Club))
	- **π**<sub>nombreClub, direccionEstadio, nombreEstadio, direccionEstadio</sub> (Estadio **|𝑥|** infoClub)

2. Listar datos personales de jugadores actuales del club River Plate que hayan jugado en el club Boca Juniors.
	
	- codRiver **⇐** **π**<sub>codigoClub</sub> (**σ**<sub>nombre = "River"</sub> (Club))
	
	- codBoca **⇐** **π**<sub>codigoClub</sub> (**σ**<sub>nombre = "Boca"</sub> (Club))
	
	- actualesRiver **⇐** **π**<sub>dni, nombre, apellido, edad, codigoCiudad</sub> (Jugador **|𝑥|** (codRiver **|𝑥|** (**σ**<sub>hasta = fechaActual</sub> ClubJugador)))
	
	- exsBoca **⇐** **π**<sub>dni, nombre, apellido, edad, codigoCiudad</sub> (Jugador **|𝑥|** (codBoca **|𝑥|** (**σ**<sub>hasta > fechaActual</sub> ClubJugador)))
	
	- **π**<sub>dni, nombre, apellido, edad, codigoCiudad</sub> (actualesRiver **⋂** exsBoca)

3. Listar información de todos los clubes donde se desempeñó el jugador: Marcelo Gallardo. Indicar nombre, año de fundación y localidad del club.
	- dniGallardo **⇐** **π**<sub>dni</sub>(**σ**<sub>(nombre = Marcelo) ^ (apellido = Gallardo)</sub>(Jugador))
	
	- codClubesGallardo **⇐** **π**<sub>codigoClub</sub>(dniGallardo **|𝑥|** ClubJugador)
	
	- infoClubesGallardo **⇐** **π**<sub>codigoClub, nombre, anioFundacion, codigoCiudad</sub> (codClubesGallardo **|𝑥|** Club)
	
	- nombreCiudades **⇐** **π**<sub>Ciudad.nombre, codigoCiudad</sub>(infoClubesGallardo **|𝑥|** Ciudad)
	
	- **π**<sub>Club.nombre, anioFundacion, Ciudad.nombre</sub>(nombreCiudades **|𝑥|** infoClubesGallardo)

4. Reportar dni, nombre y apellido de aquellos jugadores que no tengan más de 25 años y jueguen en algún club de la ciudad de Junín. 

5. Mostrar el nombre de los clubes que tengan jugadores de la ciudad de Chivilcoy mayores de 25 años.

6. Reportar el nombre y apellido de aquellos jugadores que hayan jugado en todos los clubes.

7. Listar nombre de los clubes que no hayan tenido ni tengan jugadores de la ciudad de La Plata. 

8. Mostrar dni, nombre y apellido de aquellos jugadores que jugaron o juegan en el club: Club Atlético Rosario Central.

9. Eliminar al jugador cuyo dni es: 24242424.
	- ClubJugador **⇐** ClubJugador **−** (**σ**<sub>dni = 24242424</sub> (ClubJugador))
	
	- Jugador **⇐** Jugador **−** (**σ**<sub>dni = 24242424</sub> (Jugador))

### Ejercicio 6:	
**Proyecto** (codProyecto, nombrP, descripcion, fechaInicioP, fechaFinP, fechaFinEstimada, DNIResponsable(fk), equipoBackend(fk), equipoFrontend(fk)) `DNIResponsable corresponde a un empleado, equipoBackend y equipoFrontend corresponden a un equipo.`

**Equipo** (codEquipo, nombreE, descripcionTecnologias,DNILider(fk)) `DNILider corresponde a un empleado`

**Empleado** (DNI,nombre, apellido, telefono, direccion, fechaIngreso)

**Empleado_Equipo** (codEquipo,DNI, fechaInicio, fechaFin, descripcionRol)

1. Listar nombre, descripción, fecha de inicio y fecha de fin de proyectos ya finalizados que no fueron terminados antes de la fecha de fin estimada. 

	- Solución:
		- proyFinalizados **⇐** **π**<sub>nombre, descripcion, fechaInicio, fechaFinP</sub>(**σ**<sub>fechaFinP < fechaActual</sub>(Proyecto))
		- proyFinalizadosFueraDeTermino **⇐** **π**<sub>nombre, descripcion, fechaInicio, fechaFinP</sub>(**σ**<sub>fechaFinP > fechaFinEstimada</sub>(Proyecto))
		- **π**<sub>nombre, descripcion, fechaInicio, fechaFinP</sub> (proyFinalizados **⋂** proyFinalizadosFueraDeTermino)

2. Listar DNI, nombre, apellido, teléfono, dirección y fecha de ingreso de empleados que no hayan sido responsables de proyectos.

	- Solución:
		- responsables **⇐** **π**<sub>DNI, nombre, apellido, telefono, direccion, fechaIngreso</sub>(Proyecto **|𝑥|** Empleado)
		- **π**<sub>DNI, nombre, apellido, telefono, direccion, fechaIngreso</sub>(Empleado **−** noReponsables)

3. Listar DNI, nombre, apellido, teléfono y dirección de todos los empleados que trabajan en el proyecto con nombre ‘Proyecto X’. No es necesario informar responsable y líderes.

4. Listar nombre de equipo y datos personales de líderes de equipos que no tengan empleados asignados y trabajen con tecnología ‘Java’.

	- Solución:
		- lideresConEquipo **⇐** **π**<sub>codEquipo, nombreE, descripcionTecnologias, DNILider</sub> (Equipo **|𝑥|** (Empleado_Equipo))
		- lideresSinEquipo **⇐** **π**<sub>codEquipo, nombreE, descripcionTecnologias, DNILider</sub> (Equipo **−** (lideresConEquipo))
		- lideresJavaSinEquipo **⇐** **π**<sub>nombreE, DNILider</sub> (**σ**<sub>descripcionTecnologias = 'Java'</sub>(lideresSinEquipo))
		**π**<sub>nombreE, DNI, nombre, apellido, telefono, direccion, fechaIngreso</sub>(Empleado **|𝑥|** lideresJavaSinEquipo)


5. Modificar nombre, apellido y dirección del empleado con DNI: 40568965 con los datos que desee.

	

6. Listar DNI, nombre, apellido, teléfono y dirección de empleados que son responsables de proyectos pero no han sido líderes de equipo.

	- Solución
		- lideres **⇐** **π**<sub>DNI, nombre, apellido, telefono, direccion, fechaIngreso</sub> (**σ**<sub>DNI = DNILider</sub> (Empleado **𝑥** Equipo))
		- responsables **⇐** **π**<sub>DNI, nombre, apellido, telefono, direccion, fechaIngreso</sub> (**σ**<sub>DNI = DNIResponsable</sub> (Empleado **𝑥** Proyecto))
		- **π**<sub>DNI, nombre, apellido, telefono, direccion, fechaIngreso</sub> (responsables **−** lideres)

7. Listar nombre de equipo y descripción de tecnologías de equipos que hayan sido asignados como equipos frontend y backend.

	- Solución
		- equiposBackend **⇐** **π**<sub>nombreE, descripcionE</sub> (**σ**<sub>codEquipo = equipoBackend</sub> (Equipo **𝑥** Proyecto))
		
		- equiposFrontend **⇐** **π**<sub>nombreE, descripcionE</sub> (**σ**<sub>codEquipo = equipoFrontend</sub> (Equipo **𝑥** Proyecto))
		
		- **π**<sub>nombreE, descripcionE</sub> (equipoBackend **⋂** equipoFrontend)

8. Listar nombre, descripción, fecha de inicio, nombre y apellido de responsables de proyectos a finalizar durante 2019.
		
	- Solución
		- proy2019 **⇐** **π**<sub>nombreP, descripcion, fechaInicioP, DNIResponsable</sub>(**σ**<sub>fechFinP >= 1/1/2019 ^ fechFinP <= 31/12/2019</sub> (Proyecto))
		- **π**<sub>nombreP, descripcion, fechaInicioP,</sub> (**σ**<sub>DNI = DNIResponsable</sub (Empleado **𝑥** proy2019))

9. Listar nombre de equipo, descripción de tecnología y la información personal del líder, de equipos que no estén asignados a ningún proyecto aun.

	- Solución
		- equiposEnProyFrontend **⇐** **π**<sub>codEquipo, nombreE, descripcionTecnologias, DNILider</sub> (**σ**<sub>codEquipo = equipoFrontend</sub>(Equipo **𝑥** Proyecto))
		- equiposEnProyBackend **⇐** **π**<sub>codEquipo, nombreE, descripcionTecnologias, DNILider</sub> (**σ**<sub>codEquipo = equipoFrontend</sub>(Equipo **𝑥** Proyecto))
		- equiposEnProy **⇐** (equipoFrontend **⋃** equipoBackend)
		- equiposSinProy **⇐** **π**<sub>codEquipo, nombreE, descripcionTecnologias, DNILider</sub> (Equipo **−** equiposEnProy)
		- **π**<sub>nombreE, descripcionTecnologias, nombre, apellido, telefono, direccion, fechaIngreso</sub> (**σ**<sub>DNILider = DNI</sub> (equiposSinProy **𝑥** Empleado))

### Ejercicio 7

**Vehiculo** = (patente, modelo, marca, peso, km)

**Camion** = (patente, largo, max_toneladas, cant_ruedas, tiene_acoplado)

**Auto** = (patente, es_electrico, tipo_motor)

**Service** = (fecha, patente, km_service, observaciones, monto)

**Parte** = (cod_parte, nombre, precio_parte)

**Service_Parte** = (fecha, patente, cod_parte, precio)

1. Listar todos los datos de aquellos camiones que tengan entre 8 y 12 ruedas, y que hayan realizado algún service antes de los 10000 km.
	- Camiones10000 **⇐** **π**<sub>patente, largo, max_toneladas,  cant_ruedas, tiene_acoplado</sub> (**σ**<sub>(cant_ruedas > 8) ^ (cant_ruedas < 12)</sub> (Camion)) **|𝑥|** (**σ**<sub>km_service < 10000</sub> Service)
	
	- **π**<sub>patente, largo, max_toneladas, cant_ruedas, tiene_acoplado, modelo, marca, peso, km</sub> (Camiones10000 **|𝑥|** Vehiculo)
	
	
2. Listar los autos que hayan realizado el service “cambio de aceite” antes de los 13.000 km o hayan realizado el service “inspección general” que incluya la parte “filtro de combustible”.

	- autosCambioAceite **⇐**  **π**<sub>patente, es_electrico, tipo_motor</sub> (Auto **|𝑥|** (**σ**<sub>(observaciones = cambio de aceite) ^ (km_service < 13000)</sub> (Service))
	
	- autosInspeccionGeneral **⇐** **π**<sub>patente, es_electrico, tipo_motor</sub> (Auto **|𝑥|** (**σ**<sub>observaciones = "inspección general"</sub> (Service)) **|𝑥|** (**σ**<sub>nombre = filtro de combustible</sub> (Parte)))
	
	- **π**<sub>patente, es_electrico, tipo_motor</sub> (autosCambioAceite **⋃** autosInspeccionGeneral)
	

3. Dar de baja todos los camiones con más de 350.000 km.

4. Listar el nombre y precio de aquellas partes que figuren en todos los service realizados durante 2019.

	- **π**<sub>nombre, precio_parte</sub> (Parte **|𝑥|** (Service_Parte **|𝑥|** (**σ**<sub>(fecha > 1/1/2019) ^ (fecha < 31/12/2019)</sub> (Service))))

5. Listar todos los autos cuyo tipo de motor sea eléctrico. Mostrar información de patente, modelo , marca y peso.

	- **π**<sub>Vehiculo.patente, modelo, marca, peso</sub> (Vehiculo **|𝑥|** (**π**<sub>patente</sub> (**σ**<sub>es_electrico = true</sub> Auto))

6. Dar de alta una parte, cuyo nombre sea “Aleron”  y precio $3400.

	- Parte **⇐** Parte **⋃** {(3232, Aleron, 3400)}

7. Dar de baja todos los services que se realizaron al auto con patente ‘AAA564’.
	- Service_Parte **⇐** Service_Parte **−** (**σ**<sub>patente = "AAA564"</sub> (Service_Parte))
	
	- Service **⇐** Service **−** (**σ**<sub>patente = "AAA564"</sub> (Service))
	

8. Modificar el precio de las partes incrementando un 15 % dicho valor.

	- **σ**<sub>(precio_parte **⇐** (precio_parte * 1.15)) </sub> (Parte)

9. Listar todos los vehículos que hayan tenido services durante el 2019.

	- **π**<sub>patente, modelo, marca, peso, km</sub> (Vehiculo **|𝑥|**  (**π**<sub>patente</sub> (**σ**<sub>(fecha > "1/1/2019" ^ fecha < 31/12/2019)</sub> (Service)))
	
### Ejercicio 8

**Box** = (nroBox,m2, ubicación, capacidad, ocupacion) `ocupación es un numérico indicando cantidad de mascotas en el box actualmente, capacidad es una descripción.`

**Mascota** = (codMascota,nombre, edad, raza, peso, telefonoContacto)

**Veterinario** = (matricula, CUIT, nombYAp, direccion, telefono)

**Supervision** = (codMascota,nroBox, fechaEntra, fechaSale?, matricula(fk), descripcionEstadia) //fechaSale  tiene valor null si la mascota está actualmente en el box.`
	
### Ejercicio 9

**Barberia** = (codBarberia, razon_social, direccion, telefono)

**Cliente** = (nroCliente,DNI, nombYAp, direccionC, fechaNacimiento, celular)

**Barbero** = (codEmpleado,DNIB, nombYApB, direccionB, telefonoContacto, mail)

**Atencion** = (codEmpleado,Fecha,hora,codBarberia(fk), nroCliente(fk),descTratamiento, valor)

1. Listar DNIB, nombYApB, direccionB, telefonoContacto y mail de barberos que tengan atenciones con valor superior a 5000.

	- Solución
		- **π**<sub>DNIB, nombYApB, direccionB</sub> (Barbero **|𝑥|** (**σ**<sub>valor > 5000</sub> (Atención)))

2. Listar DNI, nombYAp, direccionC, fechaNacimiento y celular de clientes que tengan atenciones en la barbería con razón social: ‘Corta barba’ y también se hayan atendido en la barbería con razón social: ‘Barberia Barbara’.

	- Solución
		- clientesEnCortaBarba **⇐** **π**<sub>nroCliente, DNI, nombYAp, direccionC, fechaNacimiento, celular</sub> (Clientes **|𝑥|** Atencion **|𝑥|** (**σ**<sub>razon_social = 'Corta barba'</sub> (Barberia)))
		- clientesEnBarbara **⇐** **π**<sub>nroCliente, DNI, nombYAp, direccionC, fechaNacimiento, celular</sub> (Cliente **|𝑥|** Atencion **|𝑥|** (**σ**<sub>razon_social = 'Barberia Barbara'</sub> (Barberia)))
		- **π**<sub>nroCliente, DNI, nombYAp, direccionC, fechaNacimiento, celular</sub> (atencionEnBarbara **⋂** atencionEnCortaBarba)

3. Eliminar el cliente con DNI: 22222222.

	- Solución
		- nroEliminar **⇐** **π**<sub>nroCliente</sub> (**σ**<sub>DNI = 2222222</sub> (Cliente))
		- Atencion **⇐** Atencion **−** (**σ**<sub>nroCliente = nroEliminar</sub> (Atencion))
		- Cliente **⇐** Cliente **−** (**σ**<sub>nroCliente = nroEliminar</sub> (Cliente))

### Ejercicio 10

**Club** (IdClub,nombreClub,ciudad)

**Complejo** (IdComplejo,nombreComplejo, IdClub(fk))

**Cancha** (IdCancha,nombreCancha,IdComplejo(fk))

**Entrenador** (IdEntrenador, nombreEntrenador,fechaNacimiento, direccion)

**Entrenamiento** (IdEntrenamiento, fecha, IdEntrenador(fk), IdCancha(fk))

1. Listar nombre , fecha de nacimiento y dirección de entrenadores que hayan entrenado en la cancha “Cancha 1” y en la Cancha “Cancha 2”.

	- Solución
		- entrenadoresC1 **⇐** **π**<sub>nombre, fechaNacimiento, direccion</sub>(Entrenador **|𝑥|** (Entrenamiento **|𝑥|** (**σ**<sub>nombreCancha = 'Cancha 1'</sub> (Cancha)))
		- entrenadoresC2 **⇐** **π**<sub>nombre, fechaNacimiento, direccion</sub>(Entrenador **|𝑥|** (Entrenamiento **|𝑥|** (**σ**<sub>nombreCancha = 'Cancha 2'</sub> (Cancha)))
		- **π**<sub>nombre, fechaNacimiento, direccion</sub>(entrenadoresC1 **⋂** entrenadoresC2)
		

2. Listar todos los clubes en los que entrena el entrenador “Marcos Perez”. Informar nombre del club y ciudad.
	
	- Solución
		- **π**<sub>nombreClub, ciudad</sub> (Club **|𝑥|** (Complejo **|𝑥|** (Cancha **|𝑥|** (Entrenamiento **|𝑥|** (**σ**<sub>nombreEntrenador = 'Marcos Perez'</sub>(Entrenador)))))

3. Eliminar los entrenamientos del entrenador  ‘Juan Perez’.

	- Solución
		- entrenamientosDeJuanPerez **⇐** **π**<sub>IdEntrenamiento, fecha, IdEntrenador, IdCancha</sub>(Entrenamiento **|𝑥|** (**σ**<sub>nombreEntrenador = 'Juan Perez'</sub>(Entrenador)))
		- Entrenamiento **⇐** Entrenamiento **−** entrenamientosDeJuanPerez

<u></u>


