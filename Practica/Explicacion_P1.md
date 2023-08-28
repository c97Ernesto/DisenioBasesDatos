## Modelo Conceptual ER
### Componentes
- Entidades 
- Relaciones
- Atributos

###Entidades:
Representa un elemento u objeto del mundo real con identidad
- Se diferencia de cualquier otro objeto o cosa.
- Ejemplos
 
**Conjunto de entidades:** Representación que, a partir de las características própias de cada entidad con propiedades comunes, se resume en un núcleo.

### Atributos:
Representa una propiedad básica de una entidad o relación. Es el equivalente a un campo de un registro.

**Compuesto:** Representa a un atributo generado a partir de la combinación de varios atributos simples.

**Identificador:** Es un atributo o conjunto de atributos que permite reconocer o distinguir a una entidad de una manera unívoca dentro del conjunto de entidades.

- Compuesto: Conformado por más de un atributo.
- Externo: Conformado por atributos que pertenecen a otra endidad

**Cardinalidad:** Los atributos tienen asociado el concepto de cardinalidad. Cuando se define un atributo se debe indicar si es o no obligatorio y si puede tomar más de un valor (polivalente).

- **(1,1): monovalente - obligatorio**. La cardinalidad existe y está presente, pero solamente en éste caso no se indica en forma explícita.
- **(0,1): monovalente - no obligatorio**
- **(0,n): polivalente - no obligatorio**
- **(1,n): polivalente - obligatorio**

### Relaciones:
Representan agregaciones entre dos (binaria) o más entidades. Describen las dependencias o asociaciones entre dichas entidades.

**Conjunto de Relaciones:** Es una representación que, a partir de las características propias de cada relación existente entre dos entidades, las resume en un núcleo.

**Tipos:**

- Binarias
- Ternarias
- N-arias
- Recursiva

**Cardinalidad:** Es el nivel de correspondencia entre las entidades que se relacionan. Se debe definir el nivel mínimo de correspondencia, (cardinalidad mínima), y el nivel máximo de correspondencia (cardinalidad máxima).


###Jerarquía de Generalización:
Permiten extraer propiedades comunes de varias entidades o relaciones, y generar con ellas una super-entidad que las aglutine. Así las características compartidas son expresadas una única vez en el modelo, y los rasgos específicos de cada entidad quedan definidos en su sub-entidad.

- **Relaciones de jerarquía total**. Todos los registros (instancia individual) del supertipo (entidad principal) están especializados de alguna manera en uno de los subtipo (subentidad). 
- **Relaciones de jerarquía parcial**. No todos los registros del supertipo entán obligados a estar relacionados con un subtipo. Podría llegar a quedar un registro fuera de la jerarquía si no estuviese clasificado en alguno de los subtipos.
- **Relaciones de jerarquía exclusiva**. Cada registro del supertipo solo puede estar asociado con un solo subtipo a la vez. O es de un tipo de subtipo o del otro (no puede haber solapamiento  en la especialización).
- **Relaciones de jerarquía solapada**. Un registro del supertipo puede estar asociado con múltiples subtipos al mismo tiempo. Esto permite que las especializaciones se superpongan, lo que significa que un registros puede tener características de más de un subtipo.

**Coberturas posibles**

- **(T,E)**: total-exclusiva
- **(P,E)**: parcial-exclusiva
- **(T,S)**: total-solapada
- **(P,S)**: parcial-solapada


###Subconjuntos: 
Caso especial de las jerarquías de generalización de la que se desprende solamente una especialización. No es necesario indicar la cobertura para los conjuntos.

