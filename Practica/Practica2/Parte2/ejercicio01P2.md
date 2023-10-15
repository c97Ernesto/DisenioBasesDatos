## Modelo ER Conceptual
![ejercicio1_Conceptual](../../Practica2/Parte2/drawios-png/ejercicio01P2_Conceptual.png)

## Modelo ER Lógico
![ejercicio1_Lógico](../../Practica2/Parte2/drawios-png/ejercicio01P2_Logico.drawio.png)

## Modelo Físico Relacional

Animador = (<u>DNI</u>, nombre, apellido, direccion, fechaNacimiento, web?)

Telefono = (<u>telefono</u>)

Evento = (<u>nombre, fecha</u>, descripcion, DNI(fk), nombreTipoEvento(fk))

TipoEvento =(<u>nombre</u>)

tiene = (<u>telefono(fk), dni(fk)</u>)

actua = (<u>dni(fk), (fecha, nombre)(fk)</u>)
