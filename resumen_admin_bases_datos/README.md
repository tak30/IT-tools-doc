####Autor: Pablo López Viqueira
####Título: Cuestiones fundamentales de Administración de bases de datos

#1. Fundamentos

##1.1 Ideas fundamentales de BD. 

---------------------------------------

#2. Catálogo

##2.1 Diccionario de datos (utilidad, diferenciación con el catálogo, ...):

Los __diccionarios de datos__ son estructuras lógicas donde se reúne la información sobre los datos o los 
metadatos almacenados en la BD. Entre otros, almacenan información sobre descripciones, estructuras,
aspectos de seguridad, edición y usos de los datos, etc.).  
Han evolucionado hasta formar parte de herramientas de documentación ligadas en muchos casos a
herramientas de diseño de aplicaciones.  
Actúan como __repositorio de información__ sobre software, sistemas, datos, etc. y como fuente de
definición de esquemas de diversas bases de datos.  
Mientras que el catálogo de un SGBD es mantenido por el propio SGBD, el diccionario
de datos es mantenido por el equipo de diseño y administración de las bases de datos
almacenadas por el SGBD.

##2.2 Catálogo de un SGBD (características, funciones principales, propuesta estándar):

El __catálogo__ de un SGBD consiste en un conjunto de tablas y vistas del sistema en las que se
almacenan los metadatos necesarios para el correcto funcionamiento del SGBD.  
Almacena y mantiene información sobre usuarios, tablas, esquemas, parámetros de 
inicialización, vistas etc...

La __propuesta estándar__ define __cuatro__ estructuras lógicas:

*   __Esquema__:  
Está formado por el conjunto de tablas, vistas, aserciones, dominios y otros elementos
referentes al conjunto de caracteres utilizado. Un mismo usuario o un roll puede crear
varios esquemas (y por tanto ser propietario de los elementos que contiene).

*   __Catálogo__:  
Es un conjunto de esquemas. Cada catálogo tiene uno o más esquemas, pero uno de ellos se
denomina INFORMATION_SCHEMA, que contiene información sobre todos los esquemas del 
catálogo.

*   __Cluster__:  
Es un conjunto de catálogos. Cada usuario tiene un cluster asociado, que está formado por
todos los catálogos a los que tiene acceso.

*   __Entorno__:  
Un entorno SQL es el marco de trabajo bajo el que los datos pueden existir y las operaciones
SQL pueden realizarse.



##2.3 Estructura de los catálogos de Oracle y de SQL Server

###Oracle:

En oracle para consultar el catálogo existen una serie de vistas cuyo nombre comienza por USER_, 
ALL_ o DBA_ dependiendo de sobre qué conjunto de datos se quiere realizar la consulta, y termina por
TABLES, INDEXES, OBJETS, CONSTRAINTS, VIEWS,SEQUENCES,USERS etc  
A su vez, también existen las llamadas __tablas de rendimiento dinámico__, cuya información puede
ser consultada mediante vistas cuyo nombre comienza por V`$`. Por ejemplo: V`$`PARAMETER para los
parámetros de configuración o V`$`INSTANCE para los datos del entorno de ejecución.

###SQL Server 2008:
En SQL Server 2008 existe una base de datos especial y oculta llamada RESOURCE que almacena la
información del catálogo del sistema. Para consultar dicha base de datos se accede mediante
las vistas del sistema pertenecientes al esquema sys, en cada base de datos.

---------------------------------------

#3. Integridad

##3.1 Implementación de las condiciones de los datos mediante restricciones de integridad:

Mediante las restricciones de integridad podemos asegurar las condiciones de los datos
que vamos a mantener. Existen una serie de condiciones principales que cualquier gestor
implementa específicamente:

*   Datos requeridos: Valores nulos permitidos o no.
*   Comprobación de la validez del datos: Permitir solo valores correctos, es decir,
        que cumplan una condición o condiciones específicas.
*   Integridad de entidad: Valores únicos y diferentes a nulos en la clave primaria.
*   Integridad referencial: Una clave foránea lo es de alguna clave candidata si teniendo el mismo
        dominio, o bien sus valores se corresponden con los valores de alguna clave candidata en
        la tabla a la que referencia, o bien toma valores nulos.
*   Otras condiciones: Mediante cláusulas como el "CHECK" en oracle se permite implementar
        condiciones sobre conjuntos de columnas, filas, tablas etc...
*   Consistencia: Una base de datos está en estado consistente si todos sus datos son congruentes,
        se corresponden unos con otros, de forma que no se desprenden dos interpretaciones 
        diferentes y excluyentes de un mismo hecho.


##3.2 Diferentes restricciones de integridad:

*   De clave primaria (PRIMARY KEY).
*   De clave foránea (REFERENCES).
*   De valor no nulo (NOT NULL).
*   De unicidad (UNIQUE)
*   De check (CHECK COND):
    +   Sobre un atributo.
    +   Sobre una fila.
    +   Sobre un dominio.
*   Aserciones.


##3.3 Aserciones:

Las aserciones son restricciones de integridad que implementan una condición sobre la totalidad
de la relación o que abarque a más de una relación.
Para declarar una aserción se usa la siguiente sintaxis SQL:
    
    CREATE ASSERTION nombre CHEK condicion

##3.4 Alternativas a las reglas de integridad (disparadores) y cuándo implementarlas:

Un disparador o desencadenador es un conjunto de órdenes de T-SQL que se asocian a la
ocurrencia de un evento; cuando el evento se produce, se ejecutan las ordenes.  
Los elementos fundamentales de un disparador son: Evento, Tabla y Acción.  
Los disparadores pueden ser necesarios para automatizar acciones ante modificaciones (ins, del, upd)
de una tabla o una vista. Por ejemplo:  
    ```
    CREATE TRIGGER tg_dept1_3 ON dept1  
        FOR INSERT  
        AS INSERT INTO dept1rg  
            SELECT *, getdate()  
            FROM inserted
    ```

##3.5 Estado de una restricción (integridad y transacciones):

Una restricción puede encontrarse en distintos estados:

*   ENABLE: Que se comprueba.
*   DISABLE: Que no se comprueba.

Según su estado dentro de una transacción puede ser:

*   DEFERRABLE: Que puede estar en modo de comprobación aplazada.
*   NOT DEFERRABLE: Que no puede estar en modo de comprobación aplazada.

Si una restricción es DEFERRABLE, su estado inicial dentro de una transacción puede ser: 

*   INITIALLY DEFERRED: Que comienza en modo de comprobación aplazada.
*   INITIALLY INMEDIATE: Que comienza en modo de comprobación inmediata.

##3.6 Implementación en Oracle:

*   No implementa dominios.
*   Restricciones:
    *   NOT NULL.
    *   UNIQUE. (Permite varios NULLS).
    *   PRIMARY KEY.
    *   FOREIGN KEY.
    *   CHECK.
*   No implementa ON UPDATE CASCADE.
*   Activación y desactivación de las restricciones:
    *   ENABLE VALIDATE.
    *   ENABLE NOVALIDATE.
    *   DISABLE.
*   Aplazamiento dentro de una transacción.
*   Vistas del catálogo *_CONSTRAINTS.

---------------------------------------

#4. Vistas

##4.1 Tablas y vistas (definición, diferentes tipos de cada una, ejemplos):

En BD relaciones existen __tres tipos de tablas__:

*   Tabla base: Estructura que tiene esquema y datos propios. Requiere zonas de
    almacenamiento para sus datos. Pueden ser:

    *   Persistentes: Su estructura y sus datos se mantienen, salvo acción explícita.
    *   Temporales: O su estructura o sus datos desaparecen de forma automática al finalizar
        o bien la sesión o bien la transacción que las creó.

*   Vista: Estructura que tiene esquema propio pero no datos. Sus datos son el
    resultado de la ejecución de una consulta sobre otra tabla que define la propia
    vista. Al no poseer datos no necesita de una zona de almacenamiento.

*   Vista materializada: Tiene esquema y datos propios. Los datos provienen de
    almacenar el resultado de una consulta que define la vista materializada.
    Necesita zonas de almacenamiento para sus datos.


##4.2 Actualización de vistas (condiciones y cláusula específicas involucrada):

Una vista es actualizadable si:

*   En la sentencia SELECT no aparece ni UNION, ni INTERSECT, ni EXCEPT, ni GROUP BY,
    ni HAVING.
*   La condición de definición de la vista no es un join.
*   La cláusula SELECT no incluye el término DISTINCT, ni puede incluir expresiones.
*   La cláusula FROM incluye sólo la referencia de una tabla.
*   Si en la definición de la vista aparece en el WHERE una subconsulta, en ella no
    puede aparecer una referencia en el FROM a la misma tabla que se referencia en la
    consulta principal que define la vista.

Además:

*   Si una columna de una vista está definida como una expresión, la columna no permite
    UPDATE ni la vista INSERT.
*   Si una vista no incluye alguan columna existente en la tabla definida con la propiedad
    NOT NULL, entonces no es posible un INSERT en dicha vista.

Con la cláusula WITH CHECK OPTION evitamos que una vista pueda ser actualizada pero que los
cambios no se reflejen en la vista ya que dichos cambios violan las condiciones de generación
de la propia vista.

##4.3 Utilidad de las vistas:

*   Independencia lógica de los datos.
*   Definición de esquemas propios.
*   Simplificación de las consultas.
*   Establecer condiciones de seguridad:

    -   Vistas horizontales: Permiten ocultar determinadas filas de una tabla que cumplan
        una condición, mostrando así a ciertos roles solo las filas que deban ver.
    -   Vistas verticales: Permiten ocultar determinadas columnas de una tabla, mostrando
        así a ciertos roles solo las columnas que deban ver.
*   Establecer condiciones de integridad (obsoleta).

##4.4 Vistas materializadas (definición, escenarios de utilización, ventajas e inconvenientes):

Una vista materializada es una vista que dispone de esquema y datos propios. Los datos provienen
de la ejecución dinámica de la consulta que define la vista.

Hay __tres escenarios__ en los que habitualmente se utilizan vistas manterializadas:

*   Cuando se presentan problemas de rendimiento en consultas debidos a:

    -   Gran volumen de datos.
    -   Agregados mediante funciones de agrupamiento.
    -   Joins complejos.
*   Data warehouse o almacen de datos.
*   Replicación: Compartir datos entre diferentes bases de datos.

### Ventajas:
    
*   Permiten mejorar el rendimiento de las consultas.

### Inconvenientes:

*   Necesitan espacio de almacenamiento como cualquier otra tabla base.
*   Potenciales problemas de sincronización entre la tabla base y la vista.

---------------------------------------

#5. Seguridad

##5.1 Requerimientos de seguridad en SGBD (autentificación de usuarios, autorización de acceso):

En un SGBD existen  __dos requerimientos de seguridad__ principales:

### Autentificación de usuarios:  
Se implementa mediante un __control de acceso__. Consta de dos partes:

*   Identificarse: Indicar su identidad al SGBD.
*   Autentificarse: Aportar datos adicionales que permitan al SGBD comprobar que el usuario es
    quien dice ser.

### Autorización de acceso a recursos:  
Se implementa mediante mecanismos que garantinzan el acceso a los datos y la protección frente
a accesos no autorizados. Se pueden dividir en __tres categorías__:

*   Direcionales: Basados en la concesión y revocación de privilegios.
*   Basados en roles: Implementan roles.
*   Obligatorios: Clasifican los datos en varios niveles de seguridad.

### Otros:

*   Protección frente a inferencia de datos.
*    Proteger la integridad de la BD.
*    Evitar la transferencia no autorizada de datos.
*    Auditoría.
*    Responsabilidad de tareas.

##5.2 Control de acceso a los recursos (caso de Oracle y de SQL Server):

###Oracle:

*   Existen varios roles predefinidos, entre ellos, CONNECT, RESOURCE y DBA.  
*   Privilegios globales o sobre el SGBD:  

        GRANT { privilegio | rol } [, ...]
        TO { usuario | rol | PUBLIC } [, ...]
        [WITH ADMIN OPTION]

        REVOKE { privilegio | rol | ALL PRIVILEGES } [, ...]
        FROM { usuario | rol | PUBLIC } [, ...]

*   Privilegios sobre objetos:  

        GRANT { privilegio | ALL } [, ...]
        ON objeto
        TO { usuario | rol | PUBLIC } [, ...]
        [WITH GRANT OPTION]

        REVOKE { privilegio | ALL } [, ...]
        ON
        objeto
        FROM { usuario | rol | PUBLIC } [, ...]


##5.3 Mecanismos discrecionales (características, otorgación y revocación de privilegios):

Se basan en la concesión y revocación de privilegios a cuentas de usuario.
Hay __dos niveles__ de asignación de privilegios:

*   Nivel del SGBD o del sistema o del servidor o de cuenta:
        
        CREATE TABLE
        CREATE ANY TABLE
        DROP TABLE
        CREATE VIEW
        AUDIT ANY

*   Nivel de objeto:

        SELECT
        DELETE
        INSERT
        UPDATE
        REFERENCES

Para __otorgar y revocar__ privilegios se usa la siguiente sintaxis:

    GRANT { lista_privilegios | ALL PRIVILEGES }
    ON objeto
    TO lista_identificadores_autorización
    [ WITH GRANT OPTION ]

    REVOKE [GRANT OPTION FOR] { lista_privilegios | ALL PRIVILEGES }
    ON
    objeto
    FROM lista_identificadores_autorización

##5.4 Mecanismos basados en roles:

Igual que podemos otorgar y revocar privilegios a usuarios, también podemos hacerlo sobre
roles.  
Un rol es un identificador de autorización utilizado para la asignación de privilegios
pero no para acceder al SGBD.

Los roles se asocian a usuarios, pudiendo estos tener más de un rol y así poseer todos los
privilegios de cada rol que tenga.


##5.5 Condiciones de seguridad utilizando vistas:

-   Vistas horizontales: Permiten ocultar determinadas filas de una tabla que cumplan
    una condición, mostrando así a ciertos roles solo las filas que deban ver.
-   Vistas verticales: Permiten ocultar determinadas columnas de una tabla, mostrando
    así a ciertos roles solo las columnas que deban ver.

---------------------------------------

#6. Optimización

##6.1 Indices (motivación, definición, características, diferentes tipos, ejemplos):

Un __índice__ es una estructura auxiliar que se almacena en disco y que contiene
los valores ordenados de una o más columnas.

Los índices son usados para acelerar el acceso a los datos en las operaciones
de consulta.

### Ventajas:

*   Evitan la exploración de toda la zona de almacenamiento de la tabla si se busca
    un porcentaje reducido del total de filas.

*   Acceder a los datos de una columna que tiene un índice por orden es mucho menos costoso
    que si no lo tuviese.

### Inconvenientes:

*   Retardan las operaciones de modificación (especialmente ante cargas masivas).
*   Ocupan espacio en disco.
*   Si el optimizador no los usa son contraproducentes.

### Tipos de índices:

*   Único / No único: Según la clave de indexación pueda o no tener valores repetidos.
*   Primario / Secundario: Según la clave de indexación sea la clave primaria o no.
*   Simple / Compuesto: Según esté definido sobre una única columna o varias.
*   Árbol-B+ / Bitmap.
*   Denso / Disperso: Según posea o no una entrada para cada fila de la tabla.
*   De agrupamiento / No agrupado: Según origine o no una ordenación de los datos de la
    tabla.

### Sintaxis y ejemplos:
    
    CREATE [UNIQUE] INDEX nombre_índice
    ON tabla (columna1[, columna2, ...])

    CREATE INDEX emp_spr ON emp(cdspr)
    CREATE UNIQUE INDEX emp_pk ON emp(cdemp)
    CREATE INDEX emp_ind2 ON emp(salario, comision)

    DROP INDEX nombre_índice
    
    DROP INDEX emp_spr

##6.2 Principales tipos de índices: árbol-B y bitmap (características):

### De árbol-B+:

*   Son adecuados si las columnas tienen alta cardinalidad.
*   La modificación de los valores de sus columnas no supone problemas.
*   No resultan muy adecuados para consultas con predicados OR y AND.
*   Son habituales en los entornos operacionales (OLTP).

### Bitmap:

*   Son adecuados si las columnas tienen baja cardinalidad.
*   La modificación de los valores de sus columnas plantea probleas.
*   Resultan muy adecuados para consultas con predicados OR y AND.
*   Son habituales en los entornos analíticos y de soporte a la decisión (OLAP).

##6.3 Elección de los índices y condiciones de utilización por el optimizador:

Oracle permite definir un índice como `VISIBLE` o `INVISIBLE` según el optimizador
pueda tenerlo en cuenta o no.

Depende de la cardinalidad.

##6.4 Índices en Oracle: 

En oracle los índices defectivos son los de __Árbol-B+__. 
Existen también unos índices llamados de __clave inversa__ que invierten los bytes
de cada columna incluida en el índice. Son útiles si se realizan modificaciones
reiteradamente sobre los mismos nodos hoja.

Los índices __bitmap__ usan una estructura de árbol pero en los nodos hoja almacenan
cadenas de bits. Para cada valor de la clave de indexación el índice almacena una lista
con los bits 1 y 0, indicando así las diferentes filas que poseen (1) o no (0) dicho
valor.

###Otros:

*   Index-Organized tables (de agrupamiento).
*   Árbol-B cluster.
*   Hash cluster.


##6.5 Proceso de optimización:

*   Obtención de la forma intermedia de la consulta:

    *   Transformar la consulta en una representación interna:
    
    Consiste en transformar la consulta expresada en un lenguaje de alto nivel, a una representación
    interna, sin las connotaciones propias del nivel externo y que se manipule mejor por el SGBD.

    *   Convertirla a forma canónica.

    Se convierte la consulta expresada en una representación interna a una forma canónica con el objetivo
    de encontrar otra representación de la misma consulta pero que sea más eficiente.

*   Obtención del plan de ejecución:

    *   Elegir procedimientos candidatos de bajo nivel.
    
    Cada operación de bajo nivel tiene asociados varios procedimientos de implementación,
    cada uno conlleva una fórmula de coste.

    *   Generar planes de consulta y elegir el de menor coste.
    
    Cada plan de consulta se genera combinando un conjunto de procedimientos de implementación
    a bajo nivel. Cada uno tiene asociado un coste y se elegirá el de menor coste.

##6.6 Reglas heurísticas y su aplicación:

La optimización basada en reglas heurísticas consiste en ordenar las operaciones del árbol
de consulta, para obtener una mejora del rendimiento, utilizando normas basadas en el ingenio
y en el sentido común.

Las fundamentales son:

*   Realizar las selecciones tan pronto como sea posible.
*   Sustituir selecciones y producto cartesiano por joins, cuando pueda realizarse.
*   Combinar secuencias de operaciones unarias, tales como selecciones o proyecciones, en una
    única selección o proyección.
*   Buscar expresiones comunes para poder evaluarlas una única vez.
*   Si hay varios joins o selecciones, realizar primero las más restrictivas.
*   Eliminar en cuanto se pueda los atributos no necesarios mediante proyecciones.

##6.7 Algoritmos de join (características, condiciones involucradas, joins en SQL Server):

*   Bucle anidado (nested loop join): Dos bucles anidados (para condiciones de desigualdad).
*   Bucle anidado por bloques (block nested loop join).
*   Bucle anidado indexado (indexed nested loop join): Si se dispone de índice se usa el índice.
*   Clasificación-fusión (sort-merge join): Si las relaciones del join están ordenadas por los
    atributos implicados en el mismo, se buscan los coincidentes y se fusiona (cond. desigualdad).
*   Función de hash (hash join): Se aplica una función de hash de forma que se particionan las tuplas.
    (No necesita índices pero si tiene los aprovecha).

En __SQL Server__:

*   Nested loop join.
*   Merge join.
*   Hash join.

##6.8 Optimización en Oracle 11 (modo de funcionamiento, acceso a tablas, operaciones de los planes de ejecución Oracle -vistas en los ejercicios-, hints principales):

Oracle 11 utiliza un optimizador basado fundamentalmente en costes (CBO).

Podemos distinguir __dos objetivos__ que puede seguir el optimizador:  

*   Rendimiento: Minimiza los recursos utilizados para acceder a todas las filas implicadas.
*   Tiempo de respuesta: Minimiza los recursos para obtener las primeras filas implicadas.

###Modos de funcionamiento:  

*   `ALL_ROWS`: Objetivo de rendimiento.
*   `FIRST_ROWS_n`: Objetivo de tiempo de respuesta.
*   `FiRST_ROWS`: Mezcla de coste y heurística con el objetivo de obtener las primeras filas.

###Acceso a las tablas:  

*   Exploración completa de la tabla:
    *   Imposibilidad de usar un índice.
    *   Gran volumen de datos.
    *   Tabla muy pequeña.
    *   Estadísticas obsoletas.
    *   Alto grado de paralelismo.
    *   Sintaxis: `/* FULL(emp) */`

*   Índice:  
    *   Único.
    *   Por rango.

*   Rowid: 
    *   Después de obtener el rowid de un índice.

###Operaciones vistas en los ejercicios:


###Hints:

*   Modo de optimización:
        
    *   `/* ALL_ROWS */`
    *   `/* FIRST_ROWS (n) */`

*   Acceso a datos:
    
    *   `/* CLUSTER (tb) */`
    *   `/* FULL(tabla) */`
    *   `/* INDEX(tabla [indice]) */`
    *   `/* NO_INDEX(tabla [indice]) */`
    *   `/* INDEX_ASC(tabla [indice])*/`

*   Orden de join:
    
    *   `/* ORDERED */`

*   Tipo de join:

    *   `/* USE_HASH(tb1 tb2) */`
    *   `/* USE_MERGE(tb1 tb2) */`
    *   `/* USE_NL(tb1 tb2) */`

---------------------------------------

#7. Diseño físico

##7.1 Motivación y factores que intervienen:

El __diseño físico__ es la descripción de la implementación física de las estructuras lógicas 
y de los datos de la BD.

El __objetivo__ del diseño físico es lograr una estructuración adecuada de los datos en 
almacenamiento secundario, garantizando que con ella se obtenga un buen rendimiento en las 
operaciones a realizar en la BD.

###Factores que intervienen:

*   Las características de los datos y del acceso a los mismos por parte de los usuarios.
*   Las prestaciones del SGBD, del hardware y del sistema operativo utilizado.
*   Las características de los datos y del acceso a los mismos por parte de los diferentes
    usuarios.

    *   Cantidad de datos inicial, crecimiento esperado y frecuencia y ámbito de actualización.
    *   Las consultas que se realizarán sobre cada tabla y su frecuencia.
    *   Los requerimientos de tiempo de consultas y transacciones.

*   Las prestaciones del SGBD, del hardware y del sistema operativo utilizado.


##7.2 Tareas involucradas en el diseño físico (especialmente instalación y parametrización, espacio de almacenamiento de la BD e implementación física de las tablas (heap, extensiones, condiciones de particionado, agrupamiento, ...):

*   Instalación del SGBD y parametrización del mismo:
    *   Preparación de la instalación: Estudiar las características del SGBD para aprovecharlas,
        decidir qué almacenamiento secundario utilizar etc...
    *   Instalación.
    *   Parametrización:
        *   Tamaño de página o bloque que usará la BD.
        *   Tamaño de memoria destinado a los distintos procesos del SGBD.
*   Traducción del modelo relacional a las características del SGBD elegido:
    *   Determinar el espacio de almacenamiento que usará la BD y sus características.
    *   Implementar las tablas base: Calcular tamaño de la extensión de cada tabla.
    *   Implementar los datos derivados: Datos calculados que interesa guardar por rendimiento.
    *   Implementar las restricciones de integridad y las reglas de negocio complejas.




##7.3 Casos de estudio: Oracle y SQL Server:


##7.4 Monitorización y ajuste:

Monitorizar el sistema consiste en comprobar que el funcionamiento del mismo es el adecuado para los 
objetivos que se persiguen.  
Si se detecta cualquier anomalía se procede a su corrección o ajuste.

Entre los elementos que suelen ser más críticos se encuentran:

*   Los tiempos de respuesta ante las operaciones de los usuarios.
*   La utilización de los recursos del sistema, fundamentalmente memoria, procesador, y sobre todo, 
    las tasas de transferencia a disco.
*   La asignación de espacios a los objetos de la BD.
*   La utilización de índices.
*   Las estadísticas de bloqueos e incidencias de concurrencia.
*   La actividad de los ficheros de log.
*   El porcentaje de llenado de las zonas de almacenamiento temporal.

---------------------------------------

#8. Transacciones y recuperación

##8.1 Transacción (concepto, propiedades y fases):

Una __transacción__ es una secuencia de operaciones que forman una unidad de ejecución
consistente de la BD. Finaliza con un COMMIT o un ROLLBACK.

### Propiedades ACID:

*   Atomicidad: Todas las operaciones se ejecutan o no se ejecuta ninguna.
*   Consistencia: Las transformaciones de una transacción preservan la consistencia de la BD.
*   Aislamiento: Las transacciones están aisladas unas de otras.
*   Durabilidad o persistencia: Una vez que una transacción se confirma, las modificaciones
    que produjo se hacen consistentes en la BD.

### Fases de una transacción:

*   __Activa__: Desde el momento que comienza, mientras realiza las modificaciones.
*   __Parcialmente completa__: Si se ha solicitado commit, pero todavía no ha consolidado los cambios.
*   __Completa__: Una vez que ha confirmado los cambios.
*   __Fracasada__: Entra en esa fase cuando se solicita un ROLLBACK o cuando se detecta un error que
    le impide continuar (se fuerza un ROLLBACK).
*   __Abortada__: Después de la anulación de todos los cambios por ella producidos.

##8.2 Fichero de log (características, tipos):

Un __fichero de log__ consiste es uno o varios ficheros de pequeño tamaño que almacenan referencias
de cada modificación realizada en la BD.

### Tipos:

*   __Logs con actualizaciones diferidas__: Se registran las modificaciones en el buffer del fichero
    de log, sin modificar los buffers de la BD hasta el momento de la confirmación.
*   __Logs con actualizaciones inmediatas__: Se registran las modificaciones en el buffer del fichero
    de log y también en los buffers de la BD, sin esperar al momento de confirmación.

##8.3 Proceso de confirmación y protocolo de escrituras anticipadas:

Dentro del proceso de confirmación tenemos cuatro principales estrategias de volcado de los buffers
a disco:

*   __No robar__: Si una página actualizada del buffer de la BD no puede escribirse al disco antes 
    de la confirmación de la transacción.
*   __Robar__: Si una página actualizada del buffer de la BD puede escribirse al disco antes de la 
    confirmación de la transacción.
*   __Forzar__: Si todas las páginas actualizadas en el buffer de la BD por una transacción, 
    se escriben a disco en cuanto se confirma la transacción.
*   __No forzar__: Si no todas las páginas actualizadas en el buffer de la BD por una transacción, 
    se escriben a disco en cuanto se confirma la transacción.

###Protocolo de escrituras anticipadas (write-ahead logging: WAL):

El buffer del fichero de log debe grabarse a disco antes de completar la confirmación de
una transacción.

Antes de modificar un dato en la BD física, las imágenes anteriores se grabarán en el fichero de log.

##8.4 Puntos de verificación (motivación, características, mecanismo de funcionamiento):

Un __Punto de verificación__ es un instante en el que se fuerza a grabar los cambios de los buffers
de la BD al disco.
Tienen como objetivo reducir el tiempo de recuperación y liberar los ficheros de log.

###Consiste en:

*   Suspender temporalmente la ejecución de las transacciones en curso.
*   Grabar los buffers del fichero de log en el fichero de log.
*   Grabar los buffers de la BD en la BD física.
*   Grabar un registro de checkpoint en el fichero de log.
*   Restaurar la ejecución de las transacciones.

##8.5 Política de salvaguarda ante fallo de dispositivo:

Implica restaurar la BD de la última copia de seguridad, y usar el fichero de log para completar la 
recuperación hasta los instantes inmediatos al error.

##8.6 Política de salvaguarda total:

*   Sincronizar tamaños de fichero de logs con tamaño de backups.
*   Los ficheros de log tienen que estar en un dispositivo de almacenamiento
    distinto del que almacena la BD y el SGBD.
*   Usar herramientas adicionales de backup.
*   Usar flashbackquery si estás en oracle.
*   Backups incrementales cada día en el momento de menor carga y completos cada 15 días.
*   Guardar los backups completos en un edificio diferente, usando almacenamiento no deteriorable
    como cinta.

##8.8 Oracle: elementos relacionados con la recuperación -también lo visto en los ejercicios-:

###Final de una transacción:

Una transacción finaliza confirmando sus cambios si:

*   Se ejecuta un commit.
*   Se ejecuta una sentencia DDL o una DCL.
*   Se produce una desconexión normal del SGBD.

Una transacción finaliza anulando sus cambios si:

*   Se produce un ROLLBACK.
*   Se produce una terminación anormal de la sesión.

###Modo AUTOCOMMIT.

Oracle trata cada DML como una transacción que intentará confirmarla tras su ejecución.

### Estados de una transacción:

*   Enable/Disable.
*   DEFERREABLE/NOT DEFERREABLE.
*   INITIALLY DEFERRED/INITIALLY INMEDIATE.

### Puntos de recuperación (SAVEPOINTS):

Los SAVEPOINT o puntos de recuperación o de restauración o de salvado parcial de una transacción, 
son marcas dentro de una transacción que permiten realizar un ROLLBACK hasta ese punto, sin 
finalizar la transacción.

Si en el curso de una transacción se define un punto de recuperación con:

    SAVEPOINT <punto de recuperación>

puede hacerse que se anulen los cambios posteriores a ese momento y que la transacción continúe mediante:

    ROLLBACK TO <punto de recuperación>

---------------------------------------

#9. Concurrencia

##9.1 Problemas de concurrencia, niveles de aislamiento y relación entre ellos:

###Problemas de concurrencia:

*   __Pérdida de actualización__: Se produce si dos transacciones realizan la lectura del mismo valor de
    un dato, y luego ambas lo modifican de forma independiente.
*   __Dependencia de actualización no confirmada (lectura sucia)__: Se produce al modificar una transacción
    un dato, acceder después otra transacción a ese dato modificado, y posteriormente anular sus cambios
    la primera transacción.
*   __Aparición de inconsistencias__: Se producen por violaciones de restricciones de integridad, debidas
    al aceder una transacción a un estado transitorio de la BD en el que no se verifica esa transacción.
*   __Problema del fantasma__: Se produce al añadir una transacción en alguna fila un dato que hace que
    cumpla una condición expresada en otra transacción concurrente.

###Niveles de aislamiento:

*   __SERIALIZABLE__: El SGBD garantiza que el efecto de la ejecución concurrente es el mismo que si la
    ejecución fuera en serie.  
    Problemas:

        *   Ninguno.

*   __REPEATABLE READ__: Sólo permite leer valores confirmados y además requiere que entre dos lecturas
    de un dato, ninguna otra transacción lo modifique.  
    Problemas:

        *   Fantasmas.

*   __READ COMMITED__: Sólo permite leer valores confirmados, pero no requiere que entre dos lecturas de
    un dato, ninguna otra transacción lo modifique.  
    Problemas:

        *   Inconsistencias.
        *   Fantasmas.

*   __READ UNCOMMITED__: Permite leer incluso valores no confirmados.  
    Problemas:

        *   No confirmado.  
        *   Inconsistencias.
        *   Fantasmas.

---------------------------------------

Licencia del documento: 
========================

#### Este obra cuyo autor es Pablo López viqueira está sujeta a la licencia Reconocimiento-CompartirIgual 4.0 Internacional de Creative Commons. Para ver una copia de esta licencia, visite <http://creativecommons.org/licenses/by-sa/4.0/deed.es_ES.>
