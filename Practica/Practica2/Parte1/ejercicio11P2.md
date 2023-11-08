#### 11. Se trata de modelar la información de clientes de una peluquería. 
De cada cliente debe modelarse la información personal: nombre, apellido, fecha de nacimiento, DNI, dirección y teléfonos, además de una descripción si es alérgico a algún componente químico. De cada cliente se tiene una ficha, la misma permite determinar las atenciones que se le realizaron al cliente. De cada atención se debe registrar fecha de atención, que peluquero lo atendió, si se le realizó tratamiento detalle del o los mismos. Se deberá indicar para cada tratamiento aplicado que componentes se utilizaron (en caso de ser necesario) y cantidad de cada componente.

Existen diferentes tratamientos. Los tratamientos se identifican por su nombre, y además se registra una descripción, precio, duración y los componentes necesarios para el mismo (algunos tratamientos pueden no requerir componentes), indicando cantidad sugerida por cada componente. De los componentes se registra código único de componente, nombre y marca que lo fabrica.

De cada peluquero se registran: DNI, apellido, nombre, domicilio detallado, teléfono de contacto.

Tenga en cuenta que el valor de los tratamientos varía con el transcurso del tiempo, se debe poder determinar cuánto pagó un cliente “x”, por el tratamiento “y” en una fecha determinada.

El cliente puede abonar la atención en efectivo, con débito o crédito, debiéndose registrar el modo de pago. Si abona con débito o crédito, debe quedar registro del número de tarjeta (número único), código de seguridad, banco y entidad que la emite (visa, mastercard,..). Si el pago es con crédito se debe registrar además la cantidad de cuotas en que realizó el pago. Los nombres de entidades emisoras y bancos son únicos.

Nota: tenga en cuenta que la tarjeta se puede usar en N pagos y en diferente cantidad de cuotas en cada caso.

### Modelo ER Conceptual
![ejercicio11_Conceptual](../../Practica2/Parte1/drawios-png/ejercicio11P2_Conceptual.drawio.png)

### Modelo ER Lógico
![ejercicio11_Lógico](../../Practica2/Parte1/drawios-png/ejercicio11P2_Logico.drawio.png)

### Modelo Físico Relacional

- Persona = (<u>dni</u>, nombre, apellido, calle, altura, piso?, dpto?)

- Componente = (<u>codComponente</u>, nombre, marca)

- Peluquero = (<u>dni</u>, telefono)

- Cliente = (<u>dni</u>, fechaNacimiento)

- Telefono = (<u>numeroTelefono</u>)

- tiene_tel = (<u>dni(fk), numeroTelefono(fk)</u>)

- Efectivo = (<u>dni(fk), constancia</u>)

- Tarjeta = (<u>numero</u>, codSguridad, banco, entidad)

- Debito = (<u>numero</u>)

- Credito = (<u>numero</u>, cuotas)

- Tratamiento = (<u>(fechaAtencion, dni)(fk), nomTratamiento</u>, descripcion, duracion, precio)

- Atencion = (<u>dni(fk), fechaAtencion</u>, precioAtencion, dniPiculero(fk))

- tiene (<u>codComponente(fk), nomTratamiento(fk), cantidad</u>)


