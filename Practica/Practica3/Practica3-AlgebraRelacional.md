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
	- viajesEn2019 **⇐** **π**<sub>RAZON_SOCIAL, direccion, telef, e-mail</sub> (Agencia **|𝑥|** (**σ**<sub>((FECHA > "1/1/2019") ^ (FECHA < "31/12/2019")</sub> Viaje)
	- viajesEn2020 **⇐** **π**<sub>RAZON_SOCIAL, direccion, telef, e-mail</sub> (Agencia **|𝑥|** (**σ**<sub>((FECHA > "1/1/2020") ^ (FECHA < "31/12/2020")</sub> Viaje)
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

10. Actualizar el teléfono del cliente con DNI: 2789655 a: 221-4400345.

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
	
### Ejercicio 9
#### Modelo Físico:
**Barberia** = (codBarberia, razon_social, direccion, telefono)

**Cliente** = (nroCliente,DNI, nombYAp, direccionC, fechaNacimiento, celular)

**Barbero** = (codEmpleado,DNIB, nombYApB, direccionB, telefonoContacto, mail)

**Atencion** = (codEmpleado,Fecha,hora,codBarberia(fk), nroCliente(fk),descTratamiento, valor)

1. Listar DNIB, nombYApB, direccionB, telefonoContacto y mail de barberos que tengan atenciones con valor superior a 5000.

2. Listar DNI, nombYAp, direccionC, fechaNacimiento y celular de clientes que tengan atenciones en la barbería con razón social: ‘Corta barba’ y también se hayan atendido en la barbería con razón social: ‘Barberia Barbara’.

3. Eliminar el cliente con DNI: 22222222.

### Ejercicio 10
#### Modelo Físico:
**Club** (IdClub,nombreClub,ciudad)

**Complejo** (IdComplejo,nombreComplejo, IdClub(fk))

**Cancha** (IdCancha,nombreCancha,IdComplejo(fk))

**Entrenador** (IdEntrenador, nombreEntrenador,fechaNacimiento, direccion)

**Entrenamiento** (IdEntrenamiento, fecha, IdEntrenador(fk), IdCancha(fk))

1- Listar nombre , fecha de nacimiento y dirección de entrenadores que hayan entrenado en la cancha “Cancha 1” y en la Cancha “Cancha 2”.

2- Listar todos los clubes en los que entrena el entrenador “Marcos Perez”. Informar nombre del club y ciudad.

3- Eliminar los entrenamientos del entrenador  ‘Juan Perez’.

<u></u>
