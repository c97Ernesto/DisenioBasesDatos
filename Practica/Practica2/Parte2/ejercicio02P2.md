## Modelo ER Conceptual
![ejercicio2_Conceptual](../../Practica2/Parte2/drawios-png/ejercicio02P2_Conceptual.png)

## Modelo ER Lógico
![ejercicio2_Lógico](../../Practica2/Parte2/drawios-png/ejercicio02P2_Logico.drawio.png)

## Modelo Físico Relacional

- Futbolista = (<u>DNI(fk)</u>, apodo, cantGoles)

- Deportista = (<u>DNI</u>, CUIT, nombre, apellido, razonSocial(fk))

- Club = (<u>razonSocial</u>, direccion, cantSocios)

- Color = (<u>color</u>)

- Posicion = (<u>posicion</u>)

- juegaCon = (<u>DNI(fk), DNICompa(fk)</u>)

- tiene = (<u>razonSocial(fk), color(fk)</u>)

- tiene1 = (<u>DNI(fk), posicion</u>)
