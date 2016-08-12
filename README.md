# Sync REST

A lo largo de este tutorial explicaremos como está estructurada una cuenta Paybook Sync. A su vez se explicará, haciendo uso de ejemplos, como podemos configurar una cuenta, organizar la información de las instituciones que sincronicemos a través de Paybook Sync además de como consultar esta información.
Todo esto lo realizaremos consumiendo el [API REST de Paybook Sync](https://www.paybook.com/sync/docs) de manera directa.

## Contenido
1. [Requerimientos](#requirements)
2. [Configuración de Postman](#postman)
3. [API Key](#api_key)
4. [Usuarios](#users)
	- [Consulta de Usuarios](#get_users)
	- [Creación de Usuarios](#create_users)
5. [Sesiones](#sessions)
6. [Catálogos de Instituciones](#catalogues)
	- [Catálogo de instituciones o sitios de producción](#sites_prod_catalogues)
	- [Catálogo de instituciones o sitios de prueba](#sites_testing_catalogues)
7. [Cuentas y Credenciales](#accounts_and_credentials)
	- [Cuentas](#accounts)
	- [Credenciales](#credentials)
	- [Creación de cuentas y credenciales](#create_accounts_and_credentials)
8. [Estatus de Sincronización](#sync_status)
9. [Transacciones](#transactions)
10. [Conclusión](#conclusion)

<div id='requirements'/>
## 1. Requerimientos:

1. Recomendado: Aplicación [Postman](https://www.getpostman.com/)
2. Recomednado: Conocimiento y manejo de [REST](http://asiermarques.com/2013/conceptos-sobre-apis-rest/).

<div id='postman'/>
## 2. Postman

Como se mencionó previamente a lo largo de este tutorial se mostrarán ejemplos acerca de como implementar el API REST de Sync. Para esto haremos uso de [Postman](https://www.getpostman.com/) un cliente HTTP que nos permitirá hacer las peticiones al API así como analizar las respuestas de manera fácil y sencilla. Usted puede descargar el archivo [quickstart.json](https://github.com/Paybook/sync-rest/blob/master/quickstart.json) e importarlo a la aplicación de Postman. Con unos cuantos clics podrá acompañarnos a lo largo del tutorial. 

***Importante:*** Si bien el tutorial está basado en Postman y es recomendado que usted cuente con la aplicación, usted puede implementar el API con la herramienta que más esté familiarizado y seguir el tutorial sin ningún problema. De ser este su escenario puede brincarse al punto [API Key](#api_key).

Configurando [quickstart.json](https://github.com/Paybook/sync-rest/blob/master/quickstart.json) en Postman:

1. Descarga el archivo [quickstart.json](https://github.com/Paybook/sync-rest/blob/master/quickstart.json)
2. Abrir aplicación Postman en tu computadora
3. En la parte superior izquierda dar clic en "Import" y posteriormente en la pestaña "Import File"
4. Dar clic en "Choos Files" y seleccionar el archivo [quickstart.json](https://github.com/Paybook/sync-rest/blob/master/quickstart.json) que hemos descargado en el punto 1. 
5. La colección se imporatará a la aplicación y la podremos visualizar del lado izquierdo con el nombre de "Sync Quickstart".

Este archivo contiene 9 peticiones preconfiguradas para ti. Estas peticiones las iremos ejecutando a lo largo del tutorial.

<p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/collection.png" width="200" height="200"></p>

Lo único que tenemos que hacer para que la colección de Postman funcione es configurar nuestra API key de Paybook Sync como una variable global en Postman. Para esto:

En la parte superior derecha dar clic en un icono con forma de ojo y posteriormente en el botón "edit" justo a lado de Globals.

<p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/gv1.png" width="120" height="120"></p>

Introduce el valor de tu API key de la siguiente manera:
	- En el campo de "key" introduce el valor "sync_api_key"
	- En el campo "value" introduce el valor de tu API key e.g. 7167a4a04f660f9131bafc949e8caode

Tu pantalla se debería de ver algo así:

<p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/gv2.png" width="200" height="200"></p>

Hecho esto hay que dar clic en "Save" y posteriormente cerrar la ventana. 

Para verificar que nuestra API key se haya agregado correctamente podemos ir nuevamente al icono con forma de ojo y dar clic. Debemos ver que el valor de nuestra API key ya se encuentra cargado como variable global de Postman como se muestra a continuación:

<p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/gv3.png" width="120" height="120"></p>

Hecho esto, has configurado Postman para hacer uso de recurso que te hemos proporcionado. Disfruta el tutorial. 

***Importante:*** en el punto de [Consulta de Usuarios](#postman_explanation) se hace una breve descripción de la interfaz y el manejo de Postman es recomendable consultarlo.

***Importante:*** en Postman encontrarás que en algunas secciones habrá valores como este:

<p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/gv4.png" width="700" height="90"></p>

Estos valores son preconfiguraciones que hemos hecho por ti. Estos valores se traducen a una URL tal cual, por ejemplo, https://sync.paybook.com/v1/users, por otro lado, o bien, algo como esto {{sync_api_key}} se traduce al API key que has configurado. De esta forma hemos configurado la mayoría de las llamadas y los parámetros que éstas requieren de tal manera que nos podamos enfocar en la implementación de Paybook Sync. Sin embargo, no pierdas de vista que al final del camino estamos consumiendo los endpoints del API y mandando los valores deacuerdo a los parámetros que nos solicita cada endpoint respectivamente.


<div id='api_key'/>
## 3. API Key

Para entender como está estructurada nuestra cuenta de Paybook Sync y con ello poder hacer consultas, actualizaciones o modificaciones sobre tu información, es necesario conocer ciertos conceptos de la estructura de la información en Sync. El primer concepto importante es tu API key.

Cuando creamos una cuenta de Paybook Sync se nos proporcionó una API key, por ejemplo:

```
api_key = 7767a4a04f990e9231bafc949e8ca08a
```

Este API key lo podemos visualizar como la contraseña o llave de acceso a los servicios de Paybook Sync. Únicamente a través de ella podremos consultar la información de las instituciones que sincronicemos así como hacer las configuraciones necesarias para que esto último pueda ser posible. 

Si Mateo es un desarrallodor que está haciendo uso de Paybook Sync. El API Key será su llave de acceso (como desarrollador) a toda la información que Paybook Sync le proporciona.

<div id='users'/>

##4. Usuarios

Mateo puede ligar a su API key usuarios. Un usuario lo podemos visualizar como una manera de organizar o clasificar las instituciones que sincronicemos. Supongamos que Mateo tiene 3 clientes:

- Una persona física: José Ochoa
- Una empresa: WalMart Querétaro
- Otra empresa: Starbucks Juriquilla


A estos clientes Mateo les está desarrollando una aplicación y para esa aplicación él requiere conocer la información bancaria de sus clientes (es por eso que Mateo está haciendo uso de Paybook Sync). Para poder hacer uso de Paybook Sync, además de su API key, Mateo necesitará un usuario y, por medio de ese usuario, el podrá sincronizar las cuentas bancarias de sus clientes,

  <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/ak_user.png" width="150" height="80"></p>

Pero una mejor práctica es hacer una clasificación correcta de la información. La información de esas cuentas bancarias, si bien Mateo la está usando para las aplicaciones que está desarrollando, no le pertenece a él. Entonces lo correcto sería que Mateo creará un usuario por cada uno de sus clientes (esos usuarios desde luego estarían ligados al API Key de Mateo y con ello a su cuenta de Paybook Sync) pero la información de las instituciones que se sincronicen, además de estar ligada a la cuenta de Mateo, estaría clasificada por usuario, es decir, sabríamos quién es el dueño de esa información,


  <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/ak_users.png" width="150" height="200"></p>

Importante, estos usuarios que Mateo ha creado ***no tienen una cuenta de Paybook, ni tampoco una cuenta de Paybook Sync***, el único que tiene una cuenta en Paybook es Mateo. Él únicamente ha creado estos usuarios para clasificar la información de su cuenta de Sync de tal manera que el pueda hacer consultas y gestionar esa información fácilmente. 

Una vez que hallamos entendido la diferencia entre nuestro API Key y nuestros usuarios podemos crear usuarios así como consultar los usuarios que están ligados a nuestra API Key.

<div id='get_users'/>
#### Consulta de usuarios:

<div id='postman_explanation'/>

En nuestra colección de Postman podemos seleccionar la primer petición "Get users linked to an API key":

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/req1.png" width="200" height="200"></p>

Una vez que hallamos seleccionado está petición podemos observar la estructura de ésta:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/postman.png" width="500" height="300"></p>

A continuación se describen brevemente los componentes básicos que estaremos analizando a lo largo del tutorial: 

1. Aquí se selcciona el método/verbo de HTTP de la consulta.
2. Aquí se selecciona la URL del API (en este caso siempre será la de Paybook Sync i.e. https://sync.paybook.com/v1/<endpoint>)
3. Este botón "Params" es para abrir la interfaz para introducir los parámetros que queramos enviar en query string (ver punto 5)
4. Este botón "Send" lo oprimiremos cada vez que queramos realizar la consulta
5. En esta sección introducimos cada uno de los parámetros a enviar en query string.
6. Body, en esta sección introducimos cada uno de los parámetros a enviar en el body (únicamente habilitado en peticiones con método/verbo POST).

Entonces una vez que hemos entendido la estructura básica de Postman, veremos que para la consulta de usuarios la petición se configura de la siguiente manera:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/get_users.png" width="600" height="100"></p>

Esto indica que estamos:

- Usando el método/verbo GET (ya que estamos consultando)
- Consultando el endpoint de Paybook Sync /users (https://sync.paybook.com/v1/users)
- Mandando como parámetro en query string nuestro api_key.

***Importante:*** si usted no está haciendo uso de Postman es importante identificar estos valores para que pueda hacer las peticiones con la herramienta de su elección.

Al momento de dar clic en "Send" se hace la petición al API. Y en la parte inferior de la pantalla (recuadro azul en la imagen) observaremos la respuesta del API, es decir, la lista de usuarios ligados a nuestra API Key:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/resp1.png" width="500" height="300"></p>

Es importante mencionar que para ver está respuesta tendremos que estar situados en la pestaña "Body" (recuadro verde). Si nuestra API key es inválida, o bien, no la enviamos como parámetro o no está vigente, en resumen, si la autenticación del desarrollador con el API no es correcta, entonces no obtendremos la lista de usuarios y obtendremos una respuesta de error como la siguiente:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/error.png" width="600" height="200"></p>

Cualquier petición que hagamos al API de Sync (en cualquier endpoint, con cualquier método o verbo, etc) si la autenticación no es correcta enviará un error como este.

Ojo, si en un principio no has creado usuarios la respuesta te debe regresar un arreglo vacio, puesto que todavía no hay usuarios ligados a tu API key, es decir,

```
{
  "code": 200,
  "status": true,
  "message": null,
  "response": []
}
```

<div id='create_users'/>
#### Creación de usuarios:

Para crear un usuario podemos seleccionar en la colección de Postman la segunda petición "Creates a new user":

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/creates_user.png" width="600" height="200"></p>

Aquí, a diferencia de la consulta de usuarios, estamos enviando los parámetros en Body, sin embargo las demás especificaciones son iguales. 

Esto indica que estamos:

- Usando el método/verbo POST (ya que estamos creando)
- Consultando el endpoint de Paybook Sync /users (https://sync.paybook.com/v1/users)
- Estamos mandando como parámetro en el body nuestro api_key así como el nombre del usuario que vamos a crear.

Y podremos ver una respuesta como la siguiente:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/resp_user.png" width="600" height="200"></p>

Entonces, sabiendo esto, lo que Mateo tendría que hacer es crear 3 usuarios, uno para José Ochoa, otro para WalMart y otro para Starbucks (como se hizo en el procedimiento previo) y el conseguiría tener la siguiente estructura:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/ak_users.png" width="150" height="200"></p>

Una vez hecho esto si el consulta los usuarios ligados a su API key (como también se hizo anteriormente) obtendría un arreglo con sus tres usuarios. 

¿Por qué no intentas tu crear algunos usarios con tu API key así como consultar la lista de usuarios ligados a ésta? Una vez que domines esto podremos continuar con los siguientes puntos del tutorial.

<div id='sessions'/>
##5. Sesiones

Otro de los conceptos que es importante que entendamos es el manejo de sesiones. Una forma de autenticarte con el API de Paybook es por medio de tu API key, haciendo uso de ella podrás hacer cualquier consulta o modificación de información de tu cuenta de Sync. Esto implica poder actualizar o consultar la información de cualquier institución de cualquier usuario que esté ligado a tu API key, y aunque esto tenga ciertos beneficios o bondades dependiendo de la lógica de tu aplicación puede ser inseguro en cierta medida. 

Otro modo de autenticarte con el API es por medio de una sesión. La diferencia particular es que una sesión va ligada directamente a un usuario y únicamente puede consultar o actualizar la información de éste (a diferencia del API key con la que puedes consultar todo). Para iniciar una sesión puedes seleccionar en Postman la petición "Inits Session":

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/inits_session.png" width="600" height="100"></p>

Y obtendremos una respuesta como la siguiente:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/resp_session.png" width="600" height="200"></p>

Dentro de los parámetros de la sesión está el token,

````
token = 4cacd8126db956631cde03f234544bf5
````

El valor de este token es el que podemos usar para autenticarnos con el API en vez de usar nuestro API key. A partir de aquí la autenticación que haremos en el tutorial será por medio del token y ***recuerda que éste únicamente podrá modificar o consultar la información del usuario con el que hayas creado la sesión***. 

***Importante:*** algunos métodos únicamente pueden autenticarse por medio del API key. Para más detalles consulta la [documentación general de Paybook Sync](https://www.paybook.com/sync/docs)

***Importante:*** Técnicamente la autenticación por medio del token es análoga a autenticarte por medio del API key y pasando como parámetro el id_user (token <--> api_key + id_user). La lógica es la misma, salvo que en la primera no se expone el valor de tu API key. 

***Importante:*** No confundir este token con el "token bancario" que es el que se utiliza al momento de sincronizar una institución cuya autenticación requiere token. El token que aquí manejamos únicamente es para autenticarse con el API. 

<div id='catalogues'/>

## 6. Catálogos

Continuando la situación de Mateo. Quedamos que Mateo tenía 3 clientes:

- Una persona física: José Ochoa
- Una empresa: WalMart
- Otra empresa: Starbucks Juriquilla

 Él ya creo un usuario para cada uno de sus clientes. Él sabe que, por ejemplo, respecto a José Ochoa necesita tener acceso a su información en el SAT (sus facturas tanto de ingresos como egresos), pero también necesita acceso partícularmente a las dos cuentas bancarias que maneja José Ochoa, Santander y Banamex, así como el cliente José Ochoa, WalMart y Starbucks tienen sus propias cuentas bancarias y registro fiscal, entonces la cuenta de Paybook Sync de Mateo podría tener la siguiente estructura:
 
   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/inst.png" width="250" height="200"></p>

Aquí podemos ver que cada usuario tendría sus propias instituciones (entiéndase instituciones como una forma de referirse a los bancos o el SAT), es decir, José Ochoa tendría registrado al SAT así como su cuenta de Santander y Banamex. Walmart tendría registradas sus ***propias cuentas*** de Banamex y American Express y lo mismo para Starbucks Juriquilla con Banamex, BBVA y Banorte, pero ¿Cómo quedaría todo esto estructurado en nuestra cuenta de Paybook Sync, o bien, en la cuenta de Mateo?.

<div id='sites_catalogues'/>

#### Catálogo de Instituciones

<div id='sites_prod_catalogues'/>

Bien, lo primero que hay que entender es que Paybook Sync nos dará un catálogo de las instituciones que podemos sincronizar para nuestros usuarios,

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/sites.png" width="200" height="200"></p>

Entonces las instituciones que Mateo tendría que sincronizar para sus clientes tendrían que ser seleccionadas de este catálogo,

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/prod.png" width="500" height="300">

Entonces, como se ve en el diagrama anterior, las instituciones que Mateo quiera sincronizar para sus usuarios las tiene que obtener del conjunto de catálogo de instituciones de Paybook Sync (catálogo predefinido). Para esto podemos hacer el siguiente llamado al API (en Postman "Get testing sites (institutions)"):

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/catalogues.png" width="600" height="100"></p>

Es importante mencionar que en este caso se están consultando el conjunto de catálogos de prueba que Paybook Sync pone a nuestra disposición, es por eso que se está mandando el parámetro ***"is_test" en true***. Si se quiere consultar el conjunto de catálogos de producción únicamente hay que omitir enviar el parámetro ***"is_test"***. También note como en este caso la autentación la estamos haciendo por medio del ***token***.

La respuesta nos regresará un arreglo donde cada elemento es como el siguiente:

```
{
      "id_site": "56cf5728784806f72b8b4568",
      "id_site_organization": "56cf4ff5784806152c8b4567",
      "id_site_organization_type": "56cf4f5b784806cf028b4569",
      "name": "Normal",
      "credentials": [
        {
          "name": "username",
          "type": "text",
          "label": "Username",
          "required": true,
          "username": true,
          "validation": null
        },
        {
          "name": "password",
          "type": "password",
          "label": "Password",
          "required": true,
          "username": false,
          "validation": null
        }
      ]
    }
```

Usted puede analizar la respuesta con más detalle en Postman o en la interfaz de su agrado.

<div id='sites_testing_catalogues'/>

#### Instituciones de Prueba

A continuación se explicará brevemente los catálogos de prueba de Paybook Sync y cómo ***estos nos pueden ayudar a levantar nuestra aplicación sin la necesidad de tener credenciales reales de bancos o el SAT***. Esto puede ayudar a optimizar los tiempos de desarrollo. Leer este apartado es opcional, usted lo puede omitir e ir directamente a la siguiente [sección](#accounts_and_credentials).

***Importante:***no se preocupe a pesar de que usted haga uso de los catálogos de desarrollo el cambio a producción es sencillo por lo que esto no atrasará su desarrollo.

Para realizar pruebas es preferible usar los catálogos de prueba de Paybook Sync. Entre estos se encuentran:

1. ***Normal***. Esta institución sirve para simular la sincronización de una institución "normal", es decir, aquella que solo requiere usuario y contraseña.
2. ***Token***. Esta institución sirve para simular la sincronización de una institución que utiliza "token" como via de autenticación además de un usuario y contraseña. i.e. token bancario.
3. ***Error***. Esta institución sirve para simular un error al sincronizar cualquier institución.
4. ***Token & captcha***. Esta institución sirve para simular la sincronización de una institución que utiliza "token" y "captcha" como via de autenticación además de un usuario y contraseña.
5. ***Multiple Image***. Esta institución sirve para simular la sincronización de una institución que utiliza "multiple image" como via de autenticación además de un usuario y contraseña.

Por lo que Mateo, con el objetivo de realizar pruebas o levantar el desarrollo de sus aplicaciones, podría tener algo así:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/test.png" width="500" height="300"></p>

Mediante este catálogo de prueba Mateo o tu podrán, por ejemplo, simular la sincronización del SAT, recordemos que esta sincronización exige un RFC -- un usuario -- y CIEC -- una contraseña -- por lo que sincronizando una institución de prueba Normal -- que solo pide usuario y contraseña -- se puede simular la sincronziación del SAT. Del mismo modo se puede hacer para simular la sincronización de Santander, Banamex, BBVA y Banorte -- o cualquier institución -- solo haciendo uso de la institición de prueba que se le parezca considerando únicamente la manera de autenticarse con ésta. Entonces Mateo podría tener algo así:

Para su cliente José Ochoa, Mateo puede

- simular el SAT con una institución de prueba Normal
- simular Santander con una institución de prueba Normal
- simular Banamex (este pide token bancario) con una institución de prueba Token.

El mismo análisis Mateo lo tiene que hacer para las demás instituciones y usted para cada institución que quiera sincronizar. ***Recuerde: esto solo si desea usar el ambiente de pruebas de Paybook Sync.***

<div id='accounts_and_credentials'/>
##7. Cuentas y Credenciales

<div id='accounts'/>
#### Cuentas

Además de entender que Paybook nos proporciona un catálogo de las instituciones que podemos sincronizar por cada uno de nuestros usuarios. Hay algunos conceptos que hay que entender acerca de como sincronizar, o bien, gestionar las instituciones que queremos sincronizar. 

Supongamos que el cliente José Ochoa le dijo a Mateo que tenía cuentas bancarias en Santander y Mateo dijo -- claro, está perfecto -- pero el cliente nunca le dijo cuantas cuentas, o bien, el cliente omitió decirle a Mateo que cuenta con 2 razones sociales (2 RFCs). Pero bueno Mateo es un excelente desarrollador y le dijo a su cliente que no había problema y que lo iba agregar a los requerimientos de la aplicación que le está desarrollando. 

Aquí hay cuestiones importantes que Mateo o tu podrían enfrentarse. Pueden sincronizar todas las cuentas de un banco o todas las facturas del SAT de un usuario (aquí el clasificador sería la institución) y Mateo podría saber, por ejemplo, las transacciones que tiene José Ochoa en Santander o en el SAT. Pero que tal si se requiere saber los movimientos que tiene en Santander pero en una cuenta especifica o en el SAT pero en determinada razón social. 

Para resolver esta clase de cosas existe el elemento Cuentas (accounts). En realidad Mateo no está sincronizando instituciones como tal, en el fondo está sincronizando cuentas específicas (cuentas bancarias o RFCs) que pertenecen, por supuesto, a determinadas instituciones. Entonces por ejemplo en el caso de su cliente José Ochoa, Mateo tendría algo así:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/accounts.png" width="300" height="150"></p>

La forma en que Paybook Sync logra esto es por medio de las cuentas. Cada RFC, o bien, cada cuenta bancaria sería una cuenta (account) y éstas al final tendrían una relación con una institución (sitios). En resumen, toda cuenta (account) estaría relacionada con una institución o sitio (site) . Más adelante veremos que las cuentas nos ayudan principalmente para la clasificación de la información y con ello hacer consultas más especificas como en el caso que hemos presentado anteriormente con Mateo.

<div id='credentials'/>
#### Credenciales

Por otro lado se encuentran las credenciales. Las credenciales están relacionadas con las cuentas en el sentido de que cada cuenta debe tener unas credenciales y estas últimas almacenan la información necesaria para realizar la sincronización de esa cuenta especifica. La información que tienen las credenciales consiste en:

- Contraseña y password del usuario para esa institución. 
- Información acerca de la consulta del estado de la sincronización. Vía web socket o consultando un endpoint de status (esto se verá más adelante).

<div id='create_accounts_and_credentials'/>
#### Creando Cuentas y Credenciales

Veremos que, a pesar que existen cuentas y credenciales, esto no implica que Mateo tenga que crear primero una cuenta y posteriormente una credencial para cada cuenta de cada usuario que quiera sincronizar. Únicamente se tienen que crear las credenciales. Sin embargo, era importante conocer la existencia de las cuentas para efectos de consulta de información y conocer como está estructurada ésta en Paybook Sync.

***Importante:*** cada vez que creamos unas credenciales Paybook Sync se encargará de crear el objeto cuenta de manera automática y lo asociará con estas credenciales.

Una vez que ya vimos que Paybook Sync nos otorga un catálogo de instituciones para sincronizar y aprendimos a consultarlo. Independientemente de si es el catálogo de instituciones reales o instituciones de prueba, cada elemento (institución) es como el siguiente:

```
{
      "id_site": "56cf5728784806f72b8b4568",
      "id_site_organization": "56cf4ff5784806152c8b4567",
      "id_site_organization_type": "56cf4f5b784806cf028b4569",
      "name": "Normal",
      "credentials": [
        {
          "name": "username",
          "type": "text",
          "label": "Username",
          "required": true,
          "username": true,
          "validation": null
        },
        {
          "name": "password",
          "type": "password",
          "label": "Password",
          "required": true,
          "username": false,
          "validation": null
        }
      ]
    }
 ```

Los datos contenidos en este elemento los requeriremos cada que querramos sincronizar un cuenta de esa institución. Para sincronizar una institución es necesario tener:

1. Un token de sesión (o bien un API key) dependiendo de como se quiera hacer la autenticación con el API.
2. El id del sitio o institución que quieres sincronizar. Este viene como ***id_site*** en cada institución al momento de consultar el catálogo.
3. El valor de las credenciales (usuario y contraseña) para esa institución que quieras sincronizar. 

Es importante que, con respecto al punto 3, el valor de estas credenciales se debe enviar como se especifica en la propiedad credentials del elemento correspondiente en el catálogo de la institución que queremos sincronizar, para el caso de la institución Normal (el ejemplo que tenemos arriba) en el elemento del catálogo se especifica lo siguiente en la propiedad credentials:

```
[
        {
          "name": "username",
          "type": "text",
          "label": "Username",
          "required": true,
          "username": true,
          "validation": null
        },
        {
          "name": "password",
          "type": "password",
          "label": "Password",
          "required": true,
          "username": false,
          "validation": null
        }
]
```

Esto quiere decir que al crear unas credenciales de la institución Normal, el valor de estas lo tendríamos que enviar como:

```
{
	"username" : "username_value",
	"password" : "password_value"
}
```

Pero por ejemplo en el caso del SAT, el elemento del catálogo nos indica:


```
[
        {
          "name": "rfc",
          "type": "text",
          "label": "RFC",
          "required": true,
          "username": true,
          "validation": null
        },
        {
          "name": "password",
          "type": "password",
          "label": "Clave CIEC",
          "required": true,
          "username": false,
          "validation": null
        }
]
```

Esto quiere decir que al crear unas credenciales de la institución SAT, el valor de estas lo tendríamos que enviar como:

```
{
	"rfc" : "rfc_value",
	"password" : "password_value"
}
```

***Importante:*** Note que para el nombre de cada propiedad tomamos el valor que especifica el objeto credentials en la propiedad "name".


La misma lógica se tiene que realizar para sincronizar cualquier institución. En resumen el obtener la institución que queremos sincronizar del catálogo de instituciones es necesario para:

1. Obtener el id_site de esa institución
2. Obtener la estructura del objeto que usaremos para enviar los valores de las credenciales.

***Importante:*** La misma lógica aplica independientemente si es un catálogo real o de prueba, ambos tienen la misma estructura. 

***Importante:*** Al usar un catálogo de prueba siempre se tienen que enviar el valor "test" tanto en "username_value" como en "password_value" (en "bank_token_value" igual en caso de requerirse). Este valor es constante independientemente de la institución de prueba que se esté manejando.

***Importante:*** Lo único que distingue si estamos sincronizando una institución de prueba o no, es de donde obtuvimos el valor id_site, si fue del catálogo de instituciones de prueba o del catálogo de instituciones reales, además de esto no hay ninguna diferencia.  


Entonces una vez que sabemos todo esto para crear unas credenciales podemos hacer lo siguiente (en Postman "Create a new credentials"):

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/credentials.png" width="600" height="150"></p>

La creación de credenciales nos dará una respuesta como la siguiente:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/resp_cred.png" width="600" height="150"></p>

Los distintos valores que vemos en la respuesta y como estos se utilizan los analizaremos en las siguientes secciones.

Siguiendo con el escenario de Mateo. El ya tiene sus usuarios creados, ahora el tendrá que crear unas ***CREDENECIALES*** por cada ***CUENTA*** de cada ***INSTITUCIÓN O SITIO*** de cada ***USUARIO*** que quiera sincronizar. Con los requerimientos nuevos que agregó su cliente José Ochoa, y suponiendo que Walmart únicamente tiene una cuenta en Banamex y una American Express y que Starbucks Juriquilla únicamente tiene una cuenta en Banamex, BBVA y Banorte el escenario de la cuenta de Paybook Sync de Mateo sería el siguiente:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/all.png" width="500" height="400"></p>

En resumen todo quedará ligado al API key de Mateo pero de una manera organizada. En resumen Mateo tendrá:

1. Su API key (recuadro verde).
2. Un usuario por cada cliente que el tiene y estos a su vez están ligados a su API key (recuadro rosa).
3. Cada usuario tiene sus propias cuentas que a su vez están relacionadas con alguna institución o sitio. (recuadros azul, amarillo). 
4. Cada cuenta tiene unas credenciales asociadas (recuadros amarillo y morado).

Nótese como en esta estructura no están contenidos los catálogos que vimos anteriormente. Esto es por que los catálogs de instituciones no le pertenecen al usuario, estos catálogos son globales y son catálogos que ofrece Paybook Sync y no están ligados a ninguna cuenta de Paybook Sync en específico, a diferencia de los usuarios, cuentas y credenciales que si están ligados a un API key y por lo tanto a una cuenta de Paybook Sync, en este caso, la de Mateo. 

Más adelante veremos que las transacciones sincronizadas de alguna institución las podrás filtrar por usuario, por sitio o institución, por cuenta o credencial y por cualquier combinación de éstas, además de poder agregar filtros de temporalidad.

En resumen, el simple hecho de crear unas credenciales implica que vamos a sincronizar una cuenta (bancaria o del SAT) de una institución específica (un banco o el SAT) para un usuario específico (algún cliente por ejemplo) y todo esto estará ligado a tu API key (tu cuenta de Paybook Sync).

<div id='sync_status'/>
## 8. Estatus de Sincronización

Pero una vez que hemos creado unas credenciales tenemos que esperar a que Paybook valide éstas y, en caso de ser validas, realice el proceso de sincronización (traer las transacciones de esa cuenta). En este proceso puede haber distintos estados, por ejemplo:

- Las credenciales introducidas pueden ser inválidas
- Las credenciales introducidas son válidas pero se requiere de un token bancario, captcha, etc. por lo que éste se le debe solicitar al usuario. 
- Las credenciales, el token bancario, y demás fueron válidos y la cuenta se está sincronizando.
- La cuenta ha terminado de sincronizarse.

Toda está información la podemos obtener a través de los datos que el API nos regresó al momento de crear las credenciales. En el ejemplo anterior vimos que el resultado de esto era, por ejemplo:

```
{
  "code": 200,
  "status": true,
  "message": null,
  "response": {
    "id_credential": "57acf8520b212aa25b8b456a",
    "username": "t**t",
    "ws": "wss://sync.paybook.com/v1/status/57acf8520b212a835a8b4596",
    "status": "https://sync.paybook.com/v1/jobs/57acf8520b212a835a8b4596/status",
    "twofa": "https://sync.paybook.com/v1/jobs/57acf8520b212a835a8b4596/twofa"
  }
}
```

En este caso únicamente explicaremos la propiedad "status". Las demás están fuera del alcance de este tutorial, sin embargo, puedes consultar la documentación del API o bien otros tutoriales para checar su funcionamiento. 

La propiedad ***"status"*** es una URL específica que Paybook Sync nos crea para qué, a través de ella, podamos consultar precisamente eso, el estatus de la sincronización de las credenciales que hemos creado. En Postman podemos seleccionar la petición "Check credentials sync status"

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/status.png" width="600" height="150"></p>

Cuya respuesta es una serie de códigos (códigos de estatus de sincronización):

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/resp_status.png" width="600" height="250"></p>

Los diferentes estatus y su significado se pueden consultar en la [documentación general de Paybook Sync](https://www.paybook.com/sync/docs) y es trabajo del desarrollador programar su propia rutina dependiendo el tipo de estatus que obtenga, es decir, si el estatus es 401 que significa que las credenciales introducidas son iválidas, es trabajo del desarrollador programar una rutina que vuelva a pedir las credenciales y volver a consumir el endpoint de crear credenciales está vez con los valores adecuados. 

Partícularmente si hemos sincronizado una institución de prueba Normal, podemos esperar por el estatus 200 que significa que la sincronización ha terminado, por tanto, ya hay transacciones de esa cuenta en específico que podemos consultar. Esto lo analizaremos en el siguiente apartado.

<div id='transactions'/>
## 9. Consulta de Transacciones

Una vez que hemos creado credenciales, monitoreado su estatus y verficado que la sincronización haya terminado exitósamente (código 200 en status), podemos consultar las transacciones de esa cuenta en específico. En Postman podemos seleccionar la petición "Get transactions" y ejecutarla. 

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/transactions.png" width="600" height="150"></p>

Nótese como estamos consultando las transacciones filtrando por id_credential esto quiere decir que únicamente queremos consultar las transacciones que se han sincronizado para la cuenta cuyas credenciales son aquellas cuyo id es id_credential. De esta misma manera podríamos filtrar por id_site para alguna institución, id_account para alguna cuenta, etc. Igual es importante mencionar que como la autenticación se está haciendo por token de sesión y éste está relacionado con un usuario específico (aquel que se utilizó al crear la sesión) automáticamente esta consulta regresa transacciones pertenecientes a ese usuario. 

En la respuesta de esta consulta veremos un arreglo de elementos (transacciones) con la siguiente estructura:

```
{
      "id_transaction": "574755f0234283db178b4569",
      "id_source": null,
      "id_user": "57acdaa90b212af35b8b4567",
      "id_external": null,
      "id_site": "56cf5728784806f72b8b4568",
      "id_site_organization": "56cf4ff5784806152c8b4567",
      "id_site_organization_type": "56cf4f5b784806cf028b4569",
      "id_account": "57317925234283f1078b6843",
      "id_account_type": "520d3aa93b8e778e0d000000",
      "id_currency": "523a25953b8e77910e8b456c",
      "is_disable": 0,
      "description": "ACME Checking Transaction 1",
      "amount": 10,
      "currency": "MXN",
      "attachments": [
        {
          "id_attachment": "574755f0234283db178b4575",
          "id_attachment_type": "56bcdfca784806d1378b4567",
          "is_valid": 0,
          "file": "5ee8bf88b42b9b933734a229e631d3e5d716e442b8f830aac737679254c2d59e.xml",
          "mime": "text/html",
          "url": "/attachments/574755f0234283db178b4575"
        },
        {
          "id_attachment": "574755f0234283db178b4577",
          "id_attachment_type": "56bcdfca784806d1378b4568",
          "is_valid": 1,
          "file": "5ee8bf88b42b9b933734a229e631d3e5d716e442b8f830aac737679254c2d59e.pdf",
          "mime": "application/pdf",
          "url": "/attachments/574755f0234283db178b4577"
        }
      ],
      "dt_transaction": 1412485200,
      "dt_refresh": 1470956640
    }
```

Que contiene información de la transacción, ya sea bancaria o fiscal. Para terminar, es importante entender la estructura final de una cuenta. En el caso del escenario de Mateo, una vez que haya creado las credenciales y monitoreado el estado de la sincronización de cada una de las credenciales que creó, entonces el ya tendrá acceso a las transacciones de sus clientes y ya las tendrá en cierta manera clasificadas. Con esto el diagrama final que describe la estructura de la infromación en su cuenta de Paybook Sync sería el siguiente:

   <p align="center"><img src="https://github.com/Paybook/sync-rest/blob/master/src/all_t.png" width="500" height="400"></p>

No hay diferencia con el diagrama que expusimos previamente. Salvo que en éste ya se muestran las transacciones de cada una de las cuentas que Mateo ha sincronizado para cada uno de sus usuarios (recuadro rojo). Al final estas transacciones también estarán ligadas a tu API key. 

<div id='conclusion'/>
## 10. Conclusión

A través de este tutorial esperamos que hayas podido implementar el API de Paybook Sync a través de los ejemplos que te hemos proporcionado y partiendo del entendimiento de como está constituida cada cuenta de Paybook Sync.
