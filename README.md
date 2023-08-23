### Conceptos Básicos
##### Qué es una Base de Datos
Def1
Def2
**Que es un Dato?**
##### Propiedades implícitas de una Base de 
___
##### Que es un DBMS o SGBD?
**Data Base Managment System o Sistema Gerenciador de Bases de Datos**. Es una coleción de programas que permiten a los usuarios _crear y mantener_ una BD. 
Es un sistema de software de propósito general que _facilita_ los procesos de definición contrucción y manipulación de BD.
> No se encarga del diseño de una Base de Datos. Solo se crea y mantiene.

##### Objetivos de un DBMS:
- Evitar redundancia e inconsitencia de datos.
- Permitir acceso a los datos en todo momento.
- Evitar anomalías en el acceso concurrente.
- Restricción a accesos no autorizados -> seguridad.
- Suministro de almacenamiento persistente de datos (aún ante fallos).
- Integridad de los datos.
- Backups.

##### Componentes de un DBMS
- **DDL(Data definition languaje):** Conjunto de instrucciónes que permiten crear la estructura de la Base de Datos. Especifica el esquema de DB

- **DML(Data manipulation languaje):** Es el lenguaje con el cual hacemos las altas, bajas, modificaciones y constulas de los datos.
    - Procedimentales (SQL)
    - No procedimentales (QBE)
___
___
### Modelado
Colección de herramientas conceptuales para describir datos, relaciones entre ellos, semántica asociada a los datos y restricciones de consistencia.
##### Niveles de Abstracción
- Visión: ve solo los datos de interés (muchas vistas para la misma BD).
- Conceptual: _qué_ datos se almacenan en la BD y _qué_ realción existe entre ellos.
- Físico: describe _cómo_ se almacenan realmente los datos (archivos y hardware).

##### Formas de modelar
**Basado en Objetos:** 

**Basado en Registros:**

##### Etapas en las que dividimos el modelado
1. **Conceptual** (representación abstracta).
    - Genérico.
    - Alejado del tipo de DBMS.
    - Alejado del producto particular.
2. **Lógico** (represetnación en una computadora).
    - Más específico
    - Orientado a un tipo de DBMS
    - Alejado del producto particular.
3. **Físico** (determinar estructuras de almacenamiento físico).
    - Específico.
    - Orientado a un producto.
___
#### Modelo Conceptual ER
##### Componentes
- Entidades 
- Relaciones
- Atributos

**Entidades:** 
- Representa un elemento u objeto del mundo real con identidad
- Se diferencia de cualquier otro objeto o cosa.
- Ejemplos
 
**Conjunto de entidades:**
- Representación que, a partir de las características própias de cada entidad con propiedades comunes, se resume en un núcleo.

**Atributos:**
- Representa una propiedad básica de una entidad o relación.
- Equivale a un campo de un registro.

**Relaciones:**
- Representan agregaciones entre dos (binaria) o más entidades.
- Tipos
    - Binarias
    - Ternarias
    - N-arias
    - Recusriva

**Conjunto de Realaciones:**
    Es una representación que, a partir de las características propias de cada relación existente entre dos entidades, las resume en un núcleo

ads
ad
asd
Def1
Def2
ads
ad
asd
Def1
Def2
ads
ad
asd
Def1
Def2
ads
ad
asd
Def1
Def2
ads
ad
asd
Def1
Def2
