<div>
<h2 align="center">Álgebra Relacional (AR)</h2>
</div>

###Ejercicio 1:

**Cliente** (<u>idCliente</u>, nombre, apellido, DNI, telefono, direccion)

**Factura** (<u>nroTicket</u>, total, fecha, hora, idCliente(Fk))

**Detalle** (<u>nroTicket, idProducto</u>, cantidad, preciounitario)

**Producto** (<u>idProducto</u>, descripcion, precio, nombreP, stock)


1. Listar nombre, apellido, DNI, teléfono y dirección de clientes con DNI superior a 22222222.

	- **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> (**σ**<sub>DNI > "22222222"</sub> (Cliente))

2. Listar nombre, apellido, DNI, teléfono y dirección de clientes con DNI superior a 22222222 y que tengan facturas cuyo total no supere los $100000. 

	- **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> (**σ** <sub>(DNI > "22222222") ^ (total < 100000)</sub> (Cliente **|𝑥|** Factura))
	
	- **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> ((**σ**<sub>total < 100000</sub> (Factura)) **|𝑥|** (**σ**<sub>DNI > 22222222</sub> (Cliente))

3. Listar nombre, apellido, DNI, teléfono y dirección de clientes que realizaron compras durante 2020.
	- **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> (**σ**<sub>(fecha >= "1/1/2020") ^ (fecha <= "31/12/2020")</sub> (Factura)) **|𝑥|** (Cliente))

4. Listar nombre, apellido, DNI, teléfono y dirección de clientes que no realizaron compras durante 2020.
	- **π**<sub>nombre, apellido, DNI, telefono, direccion</sub> (**σ**<sub>(fecha <= "1/1/2020") ^ (fecha >= "31/12/2020")</sub> (Factura)) **|𝑥|** (Cliente))

5. Listar nombre, apellido, DNI, teléfono y dirección de clientes que solo tengan compras durante 2020.

6. Listar nombre, descripción, precio y stock de productos no vendidos.

7. Listar  nombre, apellido, DNI, teléfono y dirección de clientes que no compraron el producto con nombre ‘ProductoX’ durante 2020.

8. Listar  nombre, apellido, DNI, teléfono y dirección de clientes que compraron el producto con nombre ‘Producto A’’ y no compraron el producto con nombre ‘Producto B’.

9. Listar nroTicket, total, fecha, hora y DNI del cliente, de aquellas  facturas donde se haya comprado el producto ‘Producto C’.

10. Agregar un producto con id de producto 1000, descripción “mi producto”, precio $10000, nombreP “producto Z” y stock 1000. Se supone que el idProducto 1000 no existe.




<u></u>
