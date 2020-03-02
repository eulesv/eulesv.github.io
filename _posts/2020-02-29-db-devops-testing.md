---
layout: post
title:  "DevOps y Bases de Datos - Prueba Continua"
date:   2020-02-29 00:00:00 -0600
categories: devops db
tags: devops mssql vscode netcore "bases de datos" "unit test"
---

En el [último artículo]({% post_url 2020-02-28-db-devops-strategy %}) dimos un paso muy importante al establecer un proceso que nos permite ser más eficientes al agregar cambios más rápido a nuestra base de datos... sin embargo, estos cambios dependen de una premisa muy débil, estamos asumiendo el escenario ideal donde nadie va a realizar un cambio sin informarnos; esta premisa, aunque válida, es extremadamente optimista y podría acarrearnos problemas en el camino. Si buscamos información sobre seguridad informática, la recomendación es siempre "asumir el peor escenario"; asumir que tu ambiente ya ha sido comprometido es la apuesta más segura en el ámbito de la tecnología, y precisamente de eso se trata esta entrega.

Aun cuando lo establezcamos como una regla obligatoria de nuestro proceso de desarrollo, no podemos asumir que todos los cambios van a entrar mediante nuestro esquema controlado y poder tomar acciones defensivas es el camino recomendado. El objetivo es simple, para cada cambio que debamos aplicar debemos proveer un mecanismo para verificar que los cambios no están impactando las premisas de negocio (Debe entenderse que el requerimiento de que los cambios entren por medio del esquema de versionamiento es un requerimiento de proceso, no de negocio); ahora bien..., Cómo traemos a la realidad este requerimiento? 

Estoy a punto de hacer otra recomendación que podría ser controversial, si me conocen personalmente posiblemente me han escuchado decir que las pruebas unitarias no deberían contener lógica de base de datos, sin embargo, estoy a punto de recomendar un esquema de pruebas unitarias en *c#* que verifica llamadas a la base de datos... 

**Pruebas Unitarias**  
La definición más simple de pruebas unitarias es **una porción de código que se encarga de validar otra porción de código**, tan simple como eso... Muchos equipos toman esa definición y tratan de aplicarla a cada línea de código dentro de sus soluciones, lo cual carece totalmente de sentido; por ejemplo, si creo una prueba unitaria que verifica un método de mi solución que va a la base de datos y retorna resultados, sin ninguna premisa u objetivo, puedo con mucha confianza asegurarles que esa prueba es muy probablemente inservible. No quiero causar polémica, existen escenarios (como los que cubriremos en este artículo) donde esa es una prueba válida, pero el planteamiento de la misma debe ser diferente. Esta es la razón por la que no confío ciegamente en la métrica de [cobertura](https://es.wikipedia.org/wiki/Cobertura_de_c%C3%B3digo){:target="_blank"}, si están en un equipo que tiene una métrica de porcentaje de cobertura de código sin importar la naturaleza del código y el objetivo es seguir agregando pruebas hasta lograr la métrica... HUYAN, no miren atrás!! La métrica de cobertura de código sirve para saber qué código es inservible en nuestra solución, normalmente deberíamos preguntar si esa funcionalidad es realmente necesaria (de acuerdo a los requerimientos de negocio) y de no serlo debemos seriamente proponer eliminarla (nunca debió ser escrita para empezar) ... Lo que realmente ocurre en la mayoría de los equipos es que frente a un porcentaje bajo de la métrica se establece como objetivo crear más pruebas para validar código que probablemente nunca sea usado, un desperdicio de tiempo que podría ser utilizado para crear valor de negocio. 

**Las pruebas unitarias validan lógica de negocio**, si no tienen un "criterio de aceptación" en su requerimiento lo más probable es que ese código no requiera pruebas unitarias para validarlo, incluso si existe un criterio de aceptación explícito, existe la posibilidad que una estrategia de arquitectura cubra ese requerimiento y no necesiten agregar una toda batería de pruebas para sustentarlo. Las pruebas unitarias sólo validan código que soporta al negocio, una prueba unitaria no debería consumir una base de datos o un servicio web o REST api. Si su lógica debe consumir un servicio externo deberían pensar en utilizar un mecanismo de [mocking](https://en.wikipedia.org/wiki/Mock_object){:target="_blank"} (puedes revisar [Moq](https://github.com/moq/moq4){:target="_blank"} o [Microsoft Fakes](https://docs.microsoft.com/en-us/visualstudio/test/code-generation-compilation-and-naming-conventions-in-microsoft-fakes?view=vs-2019){:target="_blank"}); el objetivo de usar una mecanismo como este, es que normalmente no es importante probar el resultado de la dependencia de nuestro código, es mucho más importante verificar el comportamiento de nuestra solución cuando el resultado es:
- Correcto (por ejemplo, devuelve la respuesta esperada)
- Incorrecto (devuelve una respuesta desconocida)
- Erróneo (falla en lugar de retornar un resultado)
- Ausente (el servicio no responde después de cierto tiempo)  

El objetivo de la prueba unitaria en estos casos es entender cómo nuestro código va a manejarse dentro de cada una de esas situaciones.

**Pruebas Unitarias en Bases de Datos**  
Después de la discusión anterior puede que resulte muy confuso llevar el tema de pruebas unitarias al ámbito de las bases de datos; después de todo una base de datos es inherentemente una dependencia del sistema, nunca un objetivo. Las pruebas unitarias realmente se tratan de la validación del objetivo de negocio con respecto al manejo de información, mientras que la comunicación con el motor y la forma en cómo se almacene la información es totalmente irrelevante; las entidades de negocio son el corazón de nuestra solución y nosotros podemos perfectamente establecer un proceso que tiene como objetivo verificar que después de aplicar un cambio cualquiera a nuestra base de datos, seguimos en capacidad de recuperar información apegada a lo que el negocio espera, seguimos siendo capaces de crear nuevas entidades con los datos mínimos que el proceso de negocio especifica y podemos dar de baja entidades bajo las condiciones que el proceso de negocio establece.

Ahora que hemos establecido la importancia de las pruebas, debemos decidir el mecanismo de implementación; podría recomendarles muchas opciones; [tSQLt](https://tsqlt.org/){:target="_blank"} viene a mi mente como una excelente opción si están utilizando *SQL Server*, pero eso dejaría en desventaja a todos los que estén utilizando otros motores de base de datos y mi objetivo es que esta guía sirva para la mayoría de las personas, esta es la misma razón por la cual no utilizo [SQL Server Unit Tests](https://docs.microsoft.com/en-us/sql/ssdt/creating-and-defining-sql-server-unit-tests?view=sql-server-ver15) que es una característica nativa; más de alguno podrá decir en el artículo de [estrategia](% post_url 2020-02-28-db-devops-strategy %){:target="_blank"} se decidió por *propiedades extendidas* que también son una característica nativa de *SQL Server* para la cual no todos los motores tienen un equivalente... cierto... pero también se provee la alternativa, que son tablas simples, que básicamente cualquier persona con interés en este tema podría implementar (por cierto, si te interesa, acepto [pull requests](https://github.com/eulesv/DB-DevOps){:target="_blank"} con implementaciones a otras bases de datos).


Otra opción sería [DbUnit](http://www.dbunit.org/) sobre todo con proyectos como [NDbUnit](https://github.com/NDbUnit/NDbUnit){:target="_blank"} pero aparentemente ambos proyectos han tenido muy poca actividad en los últimos años; probablemente quieran echarle un vistazo y decidir por ustedes mismos. 

Personalmente, he decidido tomar un camino menos complicado (y posiblemente con menos completo); valiéndome de mstest y netcore voy a crear un conjunto de pruebas simples que me permitan validar que después de aplicar o reversar cambios mis entidades de negocio siguen cumpliendo mis requerimientos. Lo primero que debemos hacer es configurar nuestro proyecto:

```
mkdir Tests
cd .\Tests
dotnet new mstest
cd ..
dotnet new sln
dotnet sln add .\Tests
```
Esta estructura básica nos permitirá agregar pruebas unitarias que soporten cada uno de los cambios que estamos agregando en la evolución de nuestra base de datos; y ya que esta demostración está basada en *SQL Server*, este es el momento apropiado para agregar una referencia al cliente de ado.net que debo utilizar.

```
dotnet add package System.Data.SqlClient --version 4.8.1
```

Ahora debo implementar mi prueba unitaria; por defecto la plantilla de proyecto `mstest` agrega un archivo *UnitTest.cs*, el cual renombraré como *Categories.cs*, el objetivo es validar la tabla que agregamos en el artículo anterior; la verificación podría validar muchos aspectos, adición de registros con los datos de acuerdo a las especificaciones de negocio, eliminación de datos de acuerdo a los filtros esperados, etc., para ilustración crearé una validación del esquema de acuerdo a las columnas, tipos de datos y longitud de tipos de datos esperadas p0r medio de un *esquema xsd* como el siguiente:

```xml
<?xml version='1.0' standalone='yes'?>
<xs:schema id='NewDataSet' xmlns='' xmlns:xs='http://www.w3.org/2001/XMLSchema' xmlns:msdata='urn:schemas-microsoft-com:xml-msdata'>
    <xs:element name='NewDataSet' msdata:IsDataSet='true' msdata:UseCurrentLocale='true'>
        <xs:complexType>
        <xs:choice minOccurs='0' maxOccurs='unbounded'>
            <xs:element name='Categories'>
            <xs:complexType>
                <xs:sequence>
                <xs:element name='CategoryID' type='xs:int' minOccurs='0' />
                <xs:element name='CategoryName' type='xs:string' minOccurs='0' />
                <xs:element name='Description' type='xs:string' minOccurs='0' />
                <xs:element name='Picture' type='xs:base64Binary' minOccurs='0' />
                </xs:sequence>
            </xs:complexType>
            </xs:element>
        </xs:choice>
        </xs:complexType>
    </xs:element>
</xs:schema>
```
Por medio de dos objetos `DataTable`, uno creado a partir del *esquema xsd* anterior y otra obtenida al realizar una consulta al servidor que deseamos comprobar, es posible realizar la validación de que las tablas son en efecto como el negocio las espera por medio del siguiente fragmento de código:

```csharp
Assert.AreEqual(t0.Columns.Count, t1.Columns.Count, "Table structure failed validation");
foreach (DataColumn c0 in t0.Columns)
{
    if (t1.Columns.Contains(c0.ColumnName))
    {
        DataColumn c1 = t1.Columns[c0.ColumnName];
        Assert.AreEqual(c0.DataType, c1.DataType);
        Assert.AreEqual(c0.MaxLength, c1.MaxLength);
    }
    else
    {
        Assert.Fail(String.Format("Column {0} failed validation", c0.ColumnName));
        break;
    }
}
```
Si reemplazo el método predeterminado por uno llamado `Categories_VerifySchema` y utilizo el ejemplo anterior para agregar la validación puedo perfectamente ver dentro del *Test Explorer* que mi prueba es satisfactoria.

![code-testrunner](/assets/img/code-testrunner.png)

Si desean hacerlo desde la línea de comando pueden simplemente ejecutar

```
dotnet test
```

Si se preguntan cómo creé el *esquema xsd*, la respuesta es simple, cuando defino el objeto por primera vez, al menos en teoría, lo estoy haciendo de acuerdo a la especificación de la necesidad de negocio, por lo tanto una vez que pruebo el script de creación en mi servidor de pruebas, puedo obtener *esquema xsd* por medio del método [WriteXmlSchema](https://docs.microsoft.com/en-us/dotnet/api/system.data.dataset.writexmlschema?view=netframework-4.8){:target="_blank"} del objeto `DataSet`. puede no parecer correcto, pero ese es precisamente el objetivo de la prueba unitaria asegurarnos que desde la perspectiva de la aplicación el esquema sea el mismo, incluso si más adelante agregáramos una nueva columna (como veremos en el artículo de pruebas en producción) mi aplicación sólo está preparada para manejar estas columnas, por lo tanto, mi consulta siempre debería respetar este esquema.

Como pueden observar, una vez que mantenemos claro que el objetivo es validar la definición de la entidad de negocio y no el código de la consulta, nuestras pruebas toman una relevancia muy importante y es posible implementarlas en cualquiera que sea la tecnología de nuestra elección. 

Para seguir definiendo el proceso, cada cambio que sea agregado en *apply.sql* debe tener un set de pruebas para sustentarlo y nuestras pruebas deberían ser ejecutadas al aplicar los cambios y después de revertirlos. Se preguntarán, cómo es posible evaluar el esquema de una tabla después de revertir su creación? la respuesta es fácil... no es posible, debemos validar los nuevos objetos únicamente al aplicar los cambios y al realizar una reversión debemos ejecutar las pruebas unitarias de todos los objetos en nuestro modelo que deben quedar sin ninguna alteración; la forma más fácil de lograr este objetivo es por medio de categorías de pruebas que en *mstest* puede definir por medio del atributo `[TestCategory]` que podemos utilizar para clasificar qué atributos corresponden al modelo `[TestCategory("Model")]` y podemos asumir que los que no estén clasificados de esta forma pertenecen al incremento actual. Esto supone una complejidad adicional, al aceptar los cambios de una base de datos deben tener cuidado de reclasificar apropiadamente las pruebas que soportan los nuevos objetos del modelo.

Si desean experimentar con esta estrategia pueden acceder al código de ejemplo para *c#* en el siguiente repositorio: [https://github.com/eulesv/DB-DevOps](https://github.com/eulesv/DB-DevOps){:target="_blank"}


Hasta la próxima!

> Este contenido es parte de la serie [DevOps y Bases de Datos]({% post_url 2020-02-26-db-devops %}), si estás interesado en este tema asegúrate de leer toda la serie.