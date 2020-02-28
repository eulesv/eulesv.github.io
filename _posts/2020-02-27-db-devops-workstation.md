---
layout: post
title:  "DevOps y Bases de Datos - Estación de Trabajo"
date:   2020-02-27 00:00:00 -0600
categories: devops db
tags: devops mssql vscode netcore azdo docker powershell "bases de datos"
---

Puede no ser muy intuitivo, pero una estación de trabajo con los insumos necesarios es el primer paso para una estrategia de DevOps exitosa, no importa si trata de desarrollo de aplicaciones tradicionales, proyectos de internet de las cosas o desarrollo para bases de datos; los equipos de desarrolladores deben tener a la mano todas las herramientas para poder producir valor de forma continua. Esta entrega discutirá las herramientas básicas necesarias para poder empezar un esquema de DevOps para bases de datos.

Uno de mis objetivos a la hora de crear esta guía es hacerla de forma que pueda ser seguida fácilmente por la mayoría de las personas, por este motivo, trataré de utilizar en su mayoría componentes gratuitos, de fácil acceso y, de preferencia, multiplataforma.


**Plataforma DevOps**

Aunque suene confuso, el primer elemento de esta guía no es un componente de la estación de trabajo, lo primero que necesitamos es una plataforma desde donde vamos a orquestar todos los componentes y actividades, la plataforma puede ser integrada o estar conformada por múltiples productos, pero como mínimo debe permitirnos **administrar una base centralizada de código fuente y automatizar los procesos de validación y liberación**.

Mi elección acá no es ninguna sorpresa [Azure DevOps Services](https://dev.azure.com/){:target="_blank"}, un servicio diseñado para dar soporte a soluciones de principio a fin, se integra con múltiples tecnologías en múltiples plataformas, puede trabajar con esquemas on-premise, con múltiples proveedores de nube, etc., la lista es larga; si no lo han utilizado antes pueden darle una oportunidad, organizaciones de 5 o menos desarrolladores con stakeholders ilimitados son gratuitas.

Si ya cuentan con una organización el único paso a realizar es crear un nuevo *Team Project*:

![azdo-teamproject](/assets/img/azdo-teamproject.png)

Una vez que el *Team Project* ha sido creado, es hora de inicializar el repositorio, en este caso utilizando un archivo ``.gitignore`` para *Visual Studio* y un archivo ``README`` para documentación.

![azdo-initgit](/assets/img/azdo-initgit.png)

**Control de Versiones**

Este es un componente que ya está presente en la mayoría de las empresas, en mis últimos años en mi vida profesional, he visto muy pocos casos donde no se tiene un sistema de control de versiones implementado. Las opciones son muchas y cada persona tiene su  favorito, en este caso yo he decidido utilizar [Git](https://git-scm.com/){:target="_blank"}.

Las ventajas de *Git* son conocidas y no vale la pena discutirlas acá, pueden encontrar múltiples páginas en Internet describiendo las características y bondades de este sistema de control de versiones. Si deseas seguir esta guía usando otro sistema (por ejemplo, *SVN*) no hay ningún problema, siempre y cuando se decidan por un sistema de control de versiones moderno que pueda servir dentro de un esquema de integración continua todo va a funcionar.

Para crear el repositorio local de *Git*, vamos a hacer referencia al repositorio remoto que creamos en nuestra plataforma DevOps, para clonar el repo puede utilizarse el botón en la página:

![azdo-repoclone](/assets/img/azdo-repoclone.png)

O simplemente puede copiarse la dirección y clonar el repo desde la línea de comandos:

```
git clone https://robep@dev.azure.com/robep/DB%20DevOps%20E2E/_git/DB%20DevOps%20E2E
```

**Entorno de Desarrollo**

Esta es otra área donde cada persona tiene un favorito y puede existir mucho debate sobre cual es mejor y el motivo por el cual es mejor. Elijan el entorno de desarrollo con que se sientan más cómodos, asegúrense de poder personalizarlo de acuerdo a sus necesidades y hacer que haga sus tareas de desarrollo lo más fáciles posible.

Mi elección en esta categoría es [code](https://code.visualstudio.com){:target="_blank"}. Si aún no lo conocen échenle un vistazo, probablemente les sorprenderá y entenderán porqué tantas personas lo están utilizando. Como mencioné antes, cada persona tiene su editor o IDE favorito, trabajen con el mejor se acople a sus necesidades. Personalmente, mi elección se basa en las siguientes características:
- Integración con *Git*
- Integración con *Azure DevOps*
- Extensiones para administrar *SQL Server*
- Extensiones para administrar *Docker*
- Integración con frameworks de pruebas unitarias
- Terminal integrada (*powershell*/*bash*)
- Extensibilidad para añadir múltiples lenguajes
- Sintaxis coloreada y auto complemento de código (contextual)

Finalmente, es multiplataforma y puedo trabajar en un ambiente con el cual estoy familiarizado no importando donde me encuentre, podría ser en mi laptop basada en *Windows*, una máquina virtual en *Azure* o incluso en mi [Raspberry Pi]({% post_url 2020-02-25-vscode-ssh-rpi %}){:target="_blank"}.

Si desean saber cuáles son las extensiones que utilicé para implementar esta guía les dejo el listado de los id a continuación (sin ningún orden en particular), pueden buscarlas en el marketplace o pueden instalarlas directamente desde la línea de comandos con ``code --install-extension id``

| Id Extensión  | Propósito  |
|---|---|
| DotJoshJohnson.xml  | Formato de archivos *XML*  |
| eamodio.gitlens  | Características adicionales de *Git* dentro de *code*  |
| formulahendry.dotnet-test-explorer  | Interfaz para administración y ejecución de pruebas unitarias  |
| {::nomarkdown}k--kato.docomment{:/}  | Generación de comentarios de documentación en *c#*  |
| ms-azuretools.vscode-docker  | Administración de imágenes y contenedores  |
| ms-mssql.mssql  | Integración con *Microsoft SQL Server*  |
| ms-vscode.csharp  | Soporte para el lenguaje *c#*  |
| ms-vscode.powershell  | Soporte para el lenguaje *PowerShell*  |
| ms-vscode.vs-keybindings  | Atajos de teclado al estilo de *Visual Studio*  |
| msazurermtools.azurerm-vscode-tools  | Soporte para creación de plantillas *ARM*  |
| vscode-icons-team.vscode-icons  | Iconos dentro de *code*  |
| ms-vsts.team  | Integración con *Azure DevOps*  |

Ahora bien, *code* realmente sólo es un editor de texto con características avanzadas, para completar mi entorno de desarrollo debo combinarlo con otras tecnologías tanto para desarrollo como para scripting, mis opciones en estas áreas son:

**Desarrollo** -> [Netcore](https://dotnet.microsoft.com/download){:target="_blank"}  
Una tecnología multiplataforma que me permitirá escribir las pruebas unitarias. Una vez más esta elección está basada en mi experiencia, *java* también es una opción viable si se sienten más cómodos con esa tecnología.

**Scripting** -> [Powershell](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell?view=powershell-7){:target="_blank"}  
Nuevamente una tecnología multiplataforma que me permite programar acciones rápidamente y pueden ser ejecutadas directamente desde el sistema de archivos, al igual que el caso anterior, perfectamente podría ser reemplazada por otra tecnología.

En este momento quizás se estén preguntando: Por qué no simplemente *Visual Studio*? Después de todo, *Visual Studio* contiene de forma integrada todos los componentes que estoy enumerando para un ambiente de desarrollo... lo cierto es que *Visual Studio* hubiera sido mi primera opción, con más de 20 años utilizándolo es la realmente la elección más natural y lógica, sin embargo, el objetivo de esta guía es que funcione para la mayoría de las personas, si por ejemplo me baso en el esquema de [bacpacs](https://docs.microsoft.com/en-us/sql/relational-databases/data-tier-applications/data-tier-applications?view=sql-server-ver15){:target="_blank"} esta guía aplicaría sólo a *SQL Server*, si utilizo *Visual Studio*, esta guía no podría ser utilizada por personas que trabajan en *MacOs* o en *Linux*.

**Servidor de Desarrollo**

Este es un punto que seguramente será controversial; mientras muchas empresas hacen esfuerzos adicionales por controlar instancias compartidas de servidores de bases de datos para que cumplan el rol de servidores de desarrollo, la recomendación de todos los expertos es que cada desarrollador debe tener su propio ambiente para trabajar. Este es un punto que muchas veces es complicado de vender a las gerencias o a los equipos de infraestructura y seguridad, e inmediatamente surgen las siguientes preocupaciones:

- Los servidores de datos requieren características o permisos especiales que no tienen los usuarios, no es factible configurar una a una las estaciones de trabajo de los desarrolladores.
- Los servidores fuera de esquemas de instalación de actualizaciones son un riesgo de seguridad para la organización.
- No es posible verificar las políticas de seguridad si cada desarrollador tiene su propio servidor instalado
- Las estaciones de trabajo de los desarrolladores tienen recursos limitados, instalar un servidor no es viable pues afecta el día a día.
- Es más fácil si todos consolidan sus cambios en un único servidor, simplifica la integración.

El único punto que puede ser descartado de inmediato de la lista anterior es el último, si bien puede parecer más sencillo, utilizar un único servidor hace más complejo entender el impacto de nuestros cambios, las modificaciones se hacen directamente sobre las estructuras, si hay un problema se corrige de forma individual y si tenemos suerte se generan scripts para llevar la versión final al control de código... Parece funcionar, pero en realidad estamos perdiendo experiencia importante, la próxima vez que alguien haga un cambio el proceso será el mismo, prueba y error... Y ese mismo esquema de reparar el error durante la integración probablemente nos persiga hasta llegar a los ambientes productivos. Si un desarrollador hace sus cambios y experimentos en su propio ambiente y debe después integrarlos con los de su equipo, éste se verá obligado a crear su código de manera que se anticipe a los cambios, deberá diseñar pruebas que le ayuden a verificar que los cambios no están rompiendo funcionalidad y dado que esta cultura será compartida por todo el equipo, a la hora de hacer una integración se dispararán todas las pruebas existentes, con el objetivo de validar que ninguna de las funcionalidades se verá impactada.

Para aliviar un poco los otros puntos vamos a hacer algo un poco diferente, los ambientes de desarrollo de bases de datos deben ser áreas de experimentación, cada desarrollador debería tener la libertad de hacer los cambios que quiera sin temor a impactar a su equipo, debe poder crear y destruir su servidor de base de datos a voluntad y en un par de minutos, si por error el servidor se corrompe de alguna forma debería poder descartarlo y empezar de nuevo de inmediato. Esto no es una utopía, esto existe desde hace muchos años y no es más que un esquema de contenedores.

Para mi esquema voy a utilizar *Microsoft SQL Server* simplemente porque tengo experiencia con el producto, muy seguramente puedes aplicar estos mismos pasos para el motor de base de datos que está usando actualmente, sólo debes asegurarte de que tu entorno de desarrollo tenga extensiones apropiadas y buscar la imagen dentro del *registro de Docker*. Ahora si te estás preguntando: Cómo me ayuda *Docker* a responder las inquietudes que se establecieron antes? la respuesta es simple:
- Muchas empresas ya están trabajando con estrategia de contenedores, es muy posible que tus desarrolladores ya estén incluso utilizando *Docker* y lo tengan instalado.
- No existe proceso de instalación o configuración para el servidor de base de datos, simplemente se obtiene una imagen preconfigurada que ha sido creada en muchos casos por el fabricante del producto, cada vez que hay una actualización del producto, se va a liberar una nueva imagen.
- El contenedor puede potencialmente ser un riesgo de seguridad, pero ya no es una máquina dentro del dominio y los únicos accesos son aquellos que explícitamente le son atribuidos, es un ambiente aislado que lo hace más difícil de ser explotado, adicionalmente no siempre está encendido y si llegase a comprometerse, simplemente puede destruirse y se crea uno nuevo en un par de minutos.
- *Docker* fue diseñado con un uso eficiente de recursos en mente, adicionalmente, es posible iniciar o detener los contenedores en cuestión de segundos permitiendo únicamente consumir los recursos cuando estamos realmente utilizándolos.

Lo primero que necesitamos es instalar [Docker](https://www.docker.com/){:target="_blank"} dado que mi estación de trabajo está basada en *Windows* y no utilizo un sistema operativo de servidor utilizaré [Docker Desktop](https://www.docker.com/products/docker-desktop){:target="_blank"}

Para hacer las cosas interesantes voy a utilizar una imagen de *SQL Server* basada en *Ubuntu*, pueden usar *Windows* si lo desean pero es una buena excusa para poder jugar con la versión de *Linux* de *SQL Server*, *Microsoft* incluso provee una [guía detallada](https://docs.microsoft.com/en-us/sql/Linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-bash){:target="_blank"} para poder crear un contenedor basado en la imagen de Ubuntu

Deben tener en cuenta que las imágenes tienen un ciclo de vida y se actualizan constantemente, la que yo utilicé al escribir este artículo no necesariamente es la última al momento de leerlo, una vez hecha esa aclaración, yo utilicé el siguiente comando para crear mi servidor local:
```
docker pull mcr.microsoft.com/mssql/server:2019-GA-ubuntu-16.04
docker tag mcr.microsoft.com/mssql/server:2019-GA-ubuntu-16.04 mssql-Linux
docker run -e "ACCEPT_EULA=Y" -e 'MSSQL_PID=Express' -e "SA_PASSWORD=P@ssw0rd" -p 1433:1433 --name sql-dev -d mssql-Linux
```

El ``tag`` es una conveniencia, facilitará la visualización de mis contenedores dentro de *code*. La magia ocurre dentro de ``docker run`` donde las 3 variables necesarias son:

| ACCEPT_EULA| Nuestra indicación que aceptamos los términos de licenciamiento |
| MSSQL_PID | La edición de SQL Server que deseamos ejecutar, en este caso es la Express que es la edición gratuita y es excelente para ambientes de desarrollo livianos |
| SA_PASSWORD | La contraseña de nuestro usuario SA que estará habilitado por defecto en nuestro servidor | 

Cuando el contenedor ha iniciado podemos probarlo creando una nueva base de datos, observen la extensión de *Docker* en *code*, desde ahí pueden administrarse las imágenes y contenedores de forma fácil, una característica bastante conveniente si aún no están familiarizados con los comandos de *Docker*:

![code-dbscript](/assets/img/code-dbscript.png)

El script de la imagen anterior está listo, pero para ejecutarlo debo transferirlo al sistema de archivos del contenedor, esto puede resultar extraño al principio, pero el contenedor es realmente un servidor independiente con un sistema operativo y sistema de archivos completo, a menos que montemos las carpetas locales en una ruta del contenedor, las herramientas no podrán acceder a este archivo. La solución más fácil en este momento es copiar nuestro archivo local a una ruta dentro del servidor para luego ejecutarlo:

```
docker cp .\database.sql sql-dev:/tmp
docker exec -it sql-dev /opt/mssql-tools/bin/sqlcmd -U sa -i /temp/database.sql
```

Finalmente, utilizamos la extensión de *SQL Server* para conectarnos a nuestro servidor de desarrollo desde *code*

![code-query](/assets/img/code-query.png)

Eso es todo! Mi estación de trabajo está configurada con todas las herramientas necesarias y estamos listos para avanzar en esta guía, el siguiente paso es uno de los más difíciles, establecer una estrategia de versionamiento para mi base de datos.

Hasta la próxima!

> Este contenido es parte de la serie [DevOps y Bases de Datos]({% post_url 2020-02-26-db-devops %}), si estás interesado en este tema asegúrate de leer toda la serie.