## Modelo ER Conceptual
![ejercicio3_Conceptual](../../Practica2/Parte2/drawios-png/ejercicio03P2_Conceptual.png)

## Modelo ER Lógico
![ejercicio3_Lógico](../../Practica2/Parte2/drawios-png/ejercicio03P2_Logico.drawio.png)

## Modelo Físico Relacional

- Empleado = (<u>dni</u>, cuit, nombre, apellido)

- NoDocente = (<u>dni(fk)</u>, categoria)

- Docente = (<u>legajo</u>, dni(fk), codigo(fk))

- Materia = (<u>codigo</u>, descripcion)

- Area = (<u>codigo</u>, descripcion, direccion)

- Telefono = (<u>telefono</u>)

- trabaja = (<u>codigo(fk), dni(fk)</u>)

- tiene = (<u>telefono(fk)</u>, codigo(fk))
