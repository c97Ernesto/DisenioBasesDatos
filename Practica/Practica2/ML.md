<div>
<h2 align="center">Decisiones sobre el modelo Lógico</h2>
</div>


### Resolver Jerarquías
El modelo relacional no soporta el concepto de herencia; por consiguiente, las jerarquías no pueden ser representadas.

- **Total Exclusiva (T,E):** Hay tres posibilidades.

- **Total Superpuesta (T,S):** Dos posibilidades. 
	- Dejar todo o dejar solo al padre.
	- No se puede eliminar el padre.
	
- **Parcial Exclusiva (P,E):** Dos posibilidades. 
	- Dejar todo o dejar solo al padre.
	- No se puede eliminar el padre
	
- **Parcial Superpuesta (P,S):** Dos posibilidades. 
	- Dejar todo o dejar solo al padre.
	- No se puede eliminar el padre

**Opciones para resolver Jerarquías**

- _Dejar todas las entidades:_ 
	- Si las entidades hijas no tienen identificador debo bajarlo desde el padre. Caso contrario es opcional.
	
- _Dejar solo al padre:_
	- Todos los atributos de los hijos pasan al padre.
	- Deben pasar como no obligatorios (0,1). Idem las relaciones en los hijos pasan como relaciones opcionales (mínima 0).
	- Si en el hijo era un atributo identificador, debe dejar de serlo. (Nunca un identificador puede ser opcional).
	
- _Dejar solo a los hijos:_
	- Se deben bajar los atributos del padre a cada uno de sus hijos.

### Resolver Atributos Compuestos

1. Generar un **único atributo** que se convierta en la concatenación de todos los atributos simples que contiene el atributo compuesto. Esta solución es simple y sencilla de implantar, pero al unir todos los
atributos simples que forman el compuesto, se pierde la identidad de cada atributo simple.

2. Definir todos los atributos **como simples**. Permite al usuario definir cada uno de los datos en forma independiente, siendo la solución más indicada.

3. Generar una **nueva entidad**, conformada por cada uno de los atributos simples que contiene. Debe estar relacionada con la entidad a la cual pertenecía el atributo compuesto. Capta mejor la esencia del atributo, pero es una opción más compleja.

### Resolver Atributos Polivalentes
Ningún SGBD relacional permite que un atributo contenga valores múltiples determinados dinámicamente. Es decir, se puede tener un atributo con múltiples valores, pero la cantidad máxima debe ser previamente determinada.

- Se quita el atributo polivalente generando una entidad y una interrelación.

- Si todos los atributos de entidades y relaciones son atributos simples se dice que el modelo está en **Primera Forma Normal (1FN)**.
	- El modelo no tiene atributos polivalentes.

