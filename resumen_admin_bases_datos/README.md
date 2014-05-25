####Autor: Pablo López Viqueira
####Título: Cuestiones fundamentales de Administración de bases de datos

#1. Fundamentos

##1.1 Ideas fundamentales de BD (necesario conocerlas pero no para una pregunta concreta)

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

#3. Integridad:

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
*   De unicidad (UNIQUE).
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
    *   UNIQUE.
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

#4. Vistas:

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

#5. Seguridad:

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

