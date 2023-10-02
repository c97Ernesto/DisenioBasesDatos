<div>
<h2 align="center">Modelo Físico Relacional</h2>
</div>

El modelo relacional representa a una BD como una colección de archivos denominados tablas, las cuales se conforman por registros.

Cada tabla se denomina **relación**, y está integrada por filas horizontales y columnas verticales. Cada fila representa un registro del archivo y se denomina **tupla**, mientras que cada columna representa un atributo del registro

### Conversión de Entidades
**Cada entidad se transforma en una tabla**


### Conversión de Relaciones. Cardinalidad

**Cardinalidad muchos a muchos:**

La solución propuesta es independiente de la cardinalidad mínima definida (obligatoria u opcional).

- La relación N a N se convierte en tabla, conformada por los atributos que definen la CP de cada una de las entidades que relaciona.

- Si la relación contiene un atributo, éste también será un atributo de la tabla de la relación

**Cardinalidad uno a muchos:**

Hay dos alternativas posibles: puede ocurrir que la relación se transforme o no en una tabla. La decisión deberá ser tomada en función a la cardinalidad mínima definida y de las decisiones de diseño que tome el administrador de la BD.

- No se convierte en tabla la relación: 

	- Uno a muchos con participación total ( (1,1) R (1,N) )
	- Uno a muchos con participación parcial del lado de muchos ( (1,1) R (0,N) )

- Se convierte en tabla la relación
	- Uno a muchos con participación parcial del lado de uno ( (0,1) R (1,N) )
	- Uno a muchos con cobertura parcial de ambos lados ( (0,1) R (0,N) )
	
**Cardinalidad uno a uno:**

- Uno a uno con participación total de ambos lados ( (1,1) R (1,1) )
	- 

- Uno a uno con participación parcial del lado de uno ( (0,1) R (1,1) )

- Uno a uno con participación parcial de ambos lados ( (0,1) R (0,1) )

### Resolver Atributos Compuestos

- Considerar sólo los atributos individuales

- Considerar todo en un sólo atributo

### Resolver Atributos Polivalentes

- Se debe agregar una entidad y una interrelación	

