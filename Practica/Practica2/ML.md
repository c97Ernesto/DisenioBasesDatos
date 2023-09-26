<div>
<h2 align="center">Decisiones sobre el modelo Lógico</h2>
</div>


### Resolver Jerarquías
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

##
	
_Dejar todas las entidades:_ 

- Si las entidades hijas no tienen identificador debo bajarlo desde el padre. Caso contrario es opcional.
	
_Dejar solo al padre:_
	
- Todos los atributos de los hijos pasan al padre.
- Deben pasar como no obligatorios (0,1). Idem las relaciones en los hijos pasan como relaciones opcionales (mínima 0).
- Si en el hijo era un atributo identificador, debe dejar de serlo. (Nunca un identificador puede ser opcional).
	
_Dejar solo a los hijos:_

- Se deben bajar los atributos del padre a cada uno de sus hijos.

### Resolver Atributos Compuestos

- Considerar sólo los atributos individuales

- Considerar todo en un sólo atributo

### Resolver Atributos Polivalentes

- Se debe agregar una entidad y una interrelación	

