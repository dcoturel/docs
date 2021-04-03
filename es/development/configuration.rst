Configuraci&oacute;n
#############

A&uacute;n cuando las convenciones evitan la necesidad de configurar muchas cosas en CakePHP, algunas pocas requieren configuraci&oaucte;n, como las credenciales de acceso a la base de datos.

Adicionalmente, hay opciones de configuraci&oacute;n opcionales que te permiten pasar por encima de valores e implementaciones por otras m&&aacute;s adecuadas a tu aplicaci&oacute;n.

.. index:: app.php, app_local.example.php

.. index:: configuration

Configurando tu aplicaci&oacute;n
============================

La configuraci&oacute;n se almacena generalmente en archivos PHP o archivos INI, y se carga en el arranque (bootstrap) de la aplicaci&oacute;n. CakePHP viene con un archivo de configuraci&oacute;n por defecto, pero si fuera necesario, puedes agregar archivos adicionales y cargarlos en el arranque de la aplicaci&oacute;n. :php:class:`Cake\\Core\\Configure` se usa para configuraci&oacute;n global, y clases como ``Cache`` proveen m&eacute;todos como ``setConfig()`` para hacer la configuraci&oacute;n simple y transparente.

El esqueleto de la aplicaci&oacute;n provee un archivo **config/app.php** que debe contener configuraciones que no var&iacute;n a trav&eacute;s de los distintos entornos en las que la aplicaci&oacute;n es desplegada. El archivo **config/app_local.php**  deber&iacute;a contener la configuraci&oacute;n que var&iacute;a entre entornos, y que debe ser administrada mediante administraci&oacute;n de la configuraci&oacute;n, o la herramienta de despliegue. Ambos archivos pueden hacer referencia a variables de entorno a traves de la funcion ``env()`` que habilita que los valores de configuracion sean seteados a traves del entorno del servidor.

Cargando archivos de cofiguracion adicionales
---------------------------------------------

Si tu aplicacion tiene muchas opciones de configuracion, puede ser util dividir la configuracion en multiples archivos. Despues de crear dichos archivos en tu carpeta
**config/** , podes cargarlos en el archivo **bootstrap.php**::

    use Cake\Core\Configure;
    use Cake\Core\Configure\Engine\PhpConfig;

    Configure::setConfig('default', new PhpConfig());
    Configure::load('app', 'default', false);
    Configure::load('other_config', 'default');

.. _environment-variables:

Variables de entorno
====================

Muchos proveedores de entornos cloud, como Heroku, permiten definir variables de entorno para informacion de configuracion. Podes configurar CakePHP a traves de variables de configuracion en el estilo de las `12factor app <http://12factor.net/>`_.
Las variables de entorno te permiten quitar estado de tu aplicacion, haciendo que sea mas facil administrar cuando la misma se despliega en muchos entornos distintos.

Como podes ver en tu archivo **app.php**, la funcion ``env()`` se usa para leer configuracion desde el entorno, y construir la configuracion de la aplicacion.
CakePHP usa cadenas :term:`DSN` para bases de datos, logs, transportes de email y configuraciones de cache, permitiendo cambiarlas facilmente en cada entorno.

Para desarrollo local, CakePHP se apalanca en `dotenv
<https://github.com/josegonzalez/php-dotenv>`_ para permitir desarrollo local con facilidad usando variables de entorno. Usa composer para requerir esta libreria y luego hay un bloque de codigo en ``bootstrap.php`` para descomentar y poder usar esta modalidad.

Vas a ver una archivo ``config/.env.example`` en tu aplicacion. Copiando este archivo en el directorio ``config/.env`` y cambiando su contenido vas a poder configurar tu aplicacion.
    

Es importante evitar commits del archivo ``config/.env`` en el repositorio. En su lugar, hacer commits de ``config/.env.example`` como una plantilla con comodines para que todos en el equipo sepan que variables de entorno se usan y que deberia ponerse en cada una.

Una vez que se han seteado las variables de entorno, se puede usar ``env()`` para leer la informacion del entorno:

    $debug = env('APP_DEBUG', false);

El segundo valor pasado a la funcion es el valor por defecto. Este valor sera el devuelto en caso de que la variable de entorno no este seteada.

.. _general-configuration:

Configuracion general
---------------------

A continuacion describimos las variables de configuracion y como afectan a tu aplicacion.

debug
    Cambia la salida de debug de CakePHP. ``false`` = Modo produccion. No se muestran       mensajes, errores o advertencias. ``true`` = Se muestran errores y advertencias.
App.namespace
    El namespace donde se encuentran las clases de la aplicacion.

    .. note::

        Si se cambia este valor, debe actualizarse el archivo **composer.json**  to para usar este namespace.
        Adicionalmente, hay que crear un nuevo autoloader ejecuando
        ``php composer.phar dumpautoload``.

.. _core-configuration-baseurl:

App.baseUrl
    Descomentar esta definicion si **no** no se quiere usar mod_rewrite en Apache con CakePHP. No olvides, en este caso, tambien borrar el archivo .htaccess.
App.base
    El directorio base donde la aplicacion reside. Si es ``false`` el valor
    sera auto-detectado. Si no es ``false``, asegurar que el string comienza
    con`/` y no termina con `/`. Por ejemplo, `/basedir` es un valor valido de
    App.base. En otro caso, el componente AuthComponent no va a funcionar bien.
App.encoding
    Define la codificacion de la aplicacion.  Esta codificacion
    se usa para generar el juego de caracteres (charset) en la salida, y codificar entidades.
    Deberia coincidir con la base de datos.
App.webroot
    El directorio de webroot.
App.wwwRoot
    La ruta de webroot.
App.fullBaseUrl
    El nombre de dominio completo (incluyendo protocolo) apuntando a la ra&iacute;z de la
    aplicaci&oacute;n. Se usa para generar URLs absolutas. Por defecto este valor se genera
    usando la variable de entorno ``$_SERVER``. De todos modos, deber&iacute;as definirlo manualmente
    para optimizar performance o si est&aacute;s preocupado por que alguien
    manipule el encabezado ``Host``.
    En un contexto de l&iacute;nea de comando este atributo no puede ser le&iacute;do de $_SERVER,
    ya que no hay servidor web involucrado. Necesitas especificarlo
    para generar URLs absolutas, por ejemplo al enviar emails.
App.imageBaseUrl
    Ruta web apuntando al directorio de im&aacute;genes públicas del webroot. Si est&aacute;s usando
    un :term:`CDN` deber&iacute;as poner en este valor la ruta al CDN.
App.cssBaseUrl
    Ruta web apuntando al directorio de archivos css del webroot. Si est&aacute;s usando
    un :term:`CDN` deber&iacute;as poner en este valor la ruta al CDN.
App.jsBaseUrl
    Ruta web apuntando al directorio de archivos js del webroot. Si est&aacute;s usando
    un :term:`CDN` deber&iacute;as poner en este valor la ruta al CDN.
App.paths
    Configura rutas para recursos no basados en clases. Soporta las subclaves
    ``plugins``, ``templates``, ``locales``, que permiten la definici&oacute;n
    de rutas para plugins, templates y archivos de localizaci&oacute;n.
App.uploadedFilesAsObjects
    Define si los archivos subidos son representados como objetos (``true``),
    o como arreglos (``false``). Esta opci&oacute;n se trata como verdadera por default.
    Ver la secci&oacute;n :ref:`Subida de archivos <request-file-uploads>` en el cap&iacute;tulo Objetos
    request y response para m&aacute;s informaci&oacute;n.
Security.salt
    Un string aleatorio usado para hashing. Este valor tambi&eacute;n se usa como la
    HMAC salt cuando se usa encripci&oacute;n sim&eacute;trica.
Asset.timestamp
    Anexa un timestamp cuando fue mdoificado un archivo por última vez al final del nombre
    de archivo cuando se usan los helpers asociados con cada uno.
    Valores v&aacute;lidos:

    - (bool) ``false`` - No hace nada (default)
    - (bool) ``true`` - Anexa el timestamp cuando debug es ``true``
    - (string) 'force' - Siempre anexa el timestamp.
Asset.cacheTime
    Define el tiempo de cach&eacute; de los assets. Determina el encabezado http ``Cache-Control``
    ``max-age``, y el encabezado http ``Expire``.
    Acepta cualquier valor que la funci&oacute;n `strtotime function
    <http://php.net/manual/en/function.strtotime.php>`_ acepte.
    El default es ``+1 day``.

Using a CDN
-----------

Para usar un CDN para cargar los activos est&aacute;ticos, cambiar ``App.imageBaseUrl``,
``App.cssBaseUrl``, ``App.jsBaseUrl`` para apuntar a una CDN URI, por ejemplo:
``https://mycdn.example.com/`` (notar el último ``/``).

Todas las im&aacute;genes, scripts y estilos cargados v&iacute;a HtmlHelper anexar&aacute;n al principio la ruta del CDN. 
Notar que hay un uso espec&iacute;fico cuando se usan activos basados en plugins:
los plugins no usar&aacute;n el prefiijo del plugin cuando ``...BaseUrl`` es usado con URI absoluta.
Por ejemplo, por defecto:

* ``$this->Helper->assetUrl('TestPlugin.logo.png')`` resuelve a ``test_plugin/logo.png``

Si definimos ``App.imageBaseUrl`` como ``https://mycdn.example.com/``:

* ``$this->Helper->assetUrl('TestPlugin.logo.png')`` resuelve a ``https://mycdn.example.com/logo.png``.

Configuraci&oacute;n de base de datos
------------------------------

Ver :ref:`Configuraci&oacute;n de base de datos <database-configuration>` para informaci&oacute;n sobre
c&oacute;mo configurar la conexi&oacute;n de base de datos.

Configuraci&oacute;n de cach&eacute;
----------------------

Ver :ref:`Configuraci&oacute;n de cach&eacute; <cache-configuration>` para informaci&oacute;n sobre
c&oacute;mo configurar cach&eacute; en CakePHP.

Configuraci&oacute;n de errores y excepciones
--------------------------------------

Ver :ref:`Configuraci&oacute;n de errores y excepciones <error-configuration>` para
informaci&oacute;n sobre c&oacute;mo configurar errores y excepciones.

Configuraci&oacute;n de logs
---------------------

Ver :ref:`log-configuration` para informaci&oacute;n sobre logs en CakePHP.

Configuraci&oacute;n de emails
-----------------------

Ver :ref:`Configuraci&oacute;n de email <email-configuration>` para informaci&oacute;n sobre
configuraci&oacute;n de email en CakePHP.

Configuraci&oacute;n de sesiones
---------------------

Ver :ref:`session-configuration` para informaci&oacute;n sobre configuraci&oacute;n de sesiones en CakePHP.

Configuraci&oacute;n de ruteo
----------------------

Ver :ref:`Configuraci&oacute;n de rutas <routes-configuration>` para m&aacute;s informaci&oacute;n
sobre creaci&oacute;n y configuraci&oacute;n de rutas en tu aplicaci&oacute;n.

.. _additional-class-paths:

Rutas de clases adicionales
===========================

Las rutas de clases adicionales se setean a trav&eacute;s de los autoloaders que use la aplicaci&oacute;n.
Cuando se usa ``composer`` para generar el autoloader, deber&iacute;as hacer lo siguiente,
para proveer rutas fallback para los controladores de tu aplicaci&oacute;n::

    "autoload": {
        "psr-4": {
            "App\\Controller\\": "/path/to/directory/with/controller/folders/",
            "App\\": "src/"
        }
    }

El c&oacute;digo de arriba define rutas para los namespaces ``App`` and ``App\Controller``
La primera clave va a ser buscada, y si no contiene la clase/archivo la segunda
clave ser&aacute; buscada. Tambi&eacute;n pod&eacute;s mapear un namespace a varias rutas, con el
siguiente c&oacute;digo::

    "autoload": {
        "psr-4": {
            "App\\": ["src/", "/path/to/directory/"]
        }
    }

Rutas para plugins, plantillas y localizaciones
-----------------------------------------------

Como los plugins, las plantillas y las localizaciones no son clases, no pueden tener
un autoloader configurado. CakePHP provee tres variables de configuraci&oacute;n para definir
rutas adicionales para esos recursos. En tu **config/app.php** pod&eacute;s definir estas variables::

    return [
        // More configuration
        'App' => [
            'paths' => [
                'plugins' => [
                    ROOT . DS . 'plugins' . DS,
                    '/path/to/other/plugins/'
                ],
                'templates' => [
                    ROOT . DS . 'templates' . DS,
                    ROOT . DS . 'templates2' . DS
                ],
                'locales' => [
                    ROOT . DS . 'resources' . DS . 'locales' . DS
                ]
            ]
        ]
    ];

Las rutas deben terminar con un separador de directorios, o sino no funcionar&aacute;n apropiadamente.

Configuraci&oacute;n de inflexi&oacute;n
==========================

Ver :ref:`inflection-configuration` para m&aacute;s informaci&oacute;n.

Clase Configure
===============

.. php:namespace:: Cake\Core

.. php:class:: Configure

La clase Configure puede ser utilizada para guardar y devolver valores
de la aplicaci&oacute;n o en tiempo de ejecuci&oacute;n. Hay que ser cuidadoso/a, esta clase
permite que se almacene cualquier cosa en ella, para luego usarla en cualquier parte
del c&oacute;digo. Esta es una tentaci&oacute;n para romper el patr&oacute;n MVC para el cual CakePHP
fue diseñado. El principal objetivo de esta case es mantener variables centralizadas
que pueden ser compartidas por muchos objetov.
Hay que recordar y tratar de vivir acorde a "Convenci&oacute;n sobre configuraci&oacute;n" y
no terminar&aacute;s rompiendo la estructura MVC que CakePHP provee.

Escribir datos de configuraci&oacute;n
-------------------------------

.. php:staticmethod:: write($key, $value)

Usar ``write()`` para guardar en la configuraci&oacute;n de la aplicaci&oacute;n::

    Configure::write('Company.name', 'Pizza, Inc.');
    Configure::write('Company.slogan', 'Pizza for your body and soul');

.. note::

    La :term:`dot notation` usado en el par&aacute;metro ``$key`` puede ser usada
    para organizar las configuraciones en grupos l&oacute;gicos.

El ejemplo puede ser realizado en una única llamada::

    Configure::write('Company', [
        'name' => 'Pizza, Inc.',
        'slogan' => 'Pizza for your body and soul'
    ]);

Pod&eacute;s usar ``Configure::write('debug', $bool)`` para cambiar entre debug y producci&oacute;n
al vuelo.

.. note::

    Cualquier cambio de configuraci&oacute;n usando ``Configure::write()`` se hace en memoria
    y no persiste entre distintos requests.


Leer datos de configuraci&oacute;n
---------------------------

.. php:staticmethod:: read($key = null, $default = null)

Se usa para leer configuraci&oacute;nd de la aplicaci&oacute;n. Si se provee una clave, la
informaci&oacute;n de la misma es devuelta. Usando los ejemplos de write() de m&aacute;s arriba, podemos
leer la informaci&oacute;n almacenada::

    // Devuelve 'Pizza Inc.'
    Configure::read('Company.name');

    // Devuelve 'Pizza for your body and soul'
    Configure::read('Company.slogan');

    Configure::read('Company');
    // Devuelve:
    ['name' => 'Pizza, Inc.', 'slogan' => 'Pizza for your body and soul'];

    // Returns 'fallback' as Company.nope is undefined.
    Configure::read('Company.nope', 'fallback');

Si ``$key`` es nulo, todos los valores de configuraci&oacute;n son devueltos.

.. php:staticmethod:: readOrFail($key)

Lee igual que :php:meth:`Cake\\Core\\Configure::read`
pero espera encontrar un para clave/valor. Si el para clave/valor no existe,
arroja una :php:class:`RuntimeException`::

    Configure::readOrFail('Company.name');    // Devuelve: 'Pizza, Inc.'
    Configure::readOrFail('Company.geolocation');  // Arroja excepci&oacute;n

    Configure::readOrFail('Company');

    // Devuelve:
    ['name' => 'Pizza, Inc.', 'slogan' => 'Pizza for your body and soul'];

Ver si una opci&oacute;n de configuraci&oacute;n est&aacute; definida
------------------------------------------------

.. php:staticmethod:: check($key)

Verifica si una clave existe y tiene un valor no nulo::

    $exists = Configure::check('Company.name');

Borrar informaci&oacute;n de configuraci&oacute;n
-----------------------------------

.. php:staticmethod:: delete($key)

Utilizado para borrar informaci&oacute;n de configuraci&oacute;n::

    Configure::delete('Company.name');

Leer y borrar informaci&oacute;n de configuraci&oacute;n
------------------------------------------

.. php:staticmethod:: consume($key)

Lee y borra un valor de configuraci&oacute;n.

.. php:staticmethod:: consumeOrFail($key)

Lee igual que :php:meth:`Cake\\Core\\Configure::consume`
pero espera encontrar un para clave/valor. Si el para clave/valor no existe,
arroja una :php:class:`RuntimeException`::

    Configure::consumeOrFail('Company.name');    // Yields: 'Pizza, Inc.'
    Configure::consumeOrFail('Company.geolocation');  // Will throw an exception

    Configure::consumeOrFail('Company');

    // Yields:
    ['name' => 'Pizza, Inc.', 'slogan' => 'Pizza for your body and soul'];

Leer y escribir archivos de configuraci&oacute;n
=========================================

.. php:staticmethod:: setConfig($name, $engine)

CakePHP viene con dos motores de archivos de configuraci&oacute;n.
:php:class:`Cake\\Core\\Configure\\Engine\\PhpConfig` lee archivos de configuraci&oacute;n
en PHP, en el mismo formato hist&oacute;rico de la clase Configure.
:php:class:`Cake\\Core\\Configure\\Engine\\IniConfig` lee archivos de configuraci&oacute;n
de tipo INI.  Ver `Documentaci&oacute;n PHP <http://php.net/parse_ini_file>`_ para mayor
informaci&oacute;n sobre archivos INI.  Para usar un archivo de configuraci&oacute;n del núcleo,
debes anexarlo usando :php:meth:`Configure::config()`::

    use Cake\Core\Configure\Engine\PhpConfig;

    // Read config files from config
    Configure::config('default', new PhpConfig());

    // Read config files from another path.
    Configure::config('default', new PhpConfig('/path/to/your/config/files/'));

Pod&eacute;s tener múltiples motores adjuntos a Configure, cada uno leyendo distintos tipos
o fuentes de archivos de configuraci&oacute;n. Pod&eacute;s interactuar con motores adjuntos
usando unos pocos otros m&eacute;todos en la clase Configure. Para vrificar qu&eacute; aliases
de motores de configuraci&oacute;n est&aacute;n adjuntos pod&eacute;s usar :php:meth:`Configure::configured()`::

    // Arreglo de motores adjuntos.
    Configure::configured();

    // Verificar si un motor puntual est&aacute; adjunto
    Configure::configured('default');

.. php:staticmethod:: drop($name)

Tambi&eacute;n se pueden quitar motores. ``Configure::drop('default')``
remueve un alias de motor de configuraci&oacute;n. Cualquier intento futuro de usar ese alias
va a fallar::

    Configure::drop('default');

.. _loading-configuration-files:

Cargar archivos de configuraci&oacute;n
--------------------------------

.. php:staticmethod:: load($key, $config = 'default', $merge = true)

Una vez que configuraste tu motor de configuraci&oacute;n, pod&eacute;s cargar el archivo de
configuraci&oacute;n con el siguiente c&oacute;digo::

    // Load my_file.php using the 'default' engine object.
    Configure::load('my_file', 'default');

Los archivos de configuraci&oacute;n cargados se unen con la configuraci&oacute;n existente en la
clase Configure. Esto permite que pises y que agregues nuevos valores a la clase.
Seteando ``$merge`` en ``true``, nunca se pisar&aacute; la configuraci&oacute;n existente.

Crear o modificar archivos de configuraci&oacute;n
-------------------------------------------

.. php:staticmethod:: dump($key, $config = 'default', $keys = [])

Vuelca toda o un parcial de la informaci&oacute;n de configuraci&oacute;n en un archivo o sistema de almacenamiento
soportado por un motor de configuraci&oacute;n. El formato de serializaci&oacute;n es decidido por el motor definido
como $config. Por ejemplo, si el motor 'default' es  :php:class:`Cake\\Core\\Configure\\Engine\\PhpConfig`,
el archivo generado va a ser un archivo de configuraci&oacute;n PHP cargable por :php:class:`Cake\\Core\\Configure\\Engine\\PhpConfig`

Dado que el archivo 'default' es una instancia de PhpConfig, la siguiente llamada guarda.
toda la configuraci&oacute;n en `my_config.php`::

    Configure::dump('my_config', 'default');

Solo guarda la configuraci&oacute;n de manejo de errores::

    Configure::dump('error', 'default', ['Error', 'Exception']);

``Configure::dump()`` puede ser utilizado para modificar o sobreescribir archivos de
configuraci&oacute;n que se pueden leer con :php:meth:`Configure::load()`

Storing Runtime Configuration
-----------------------------

.. php:staticmethod:: store($name, $cacheConfig = 'default', $data = null)

You can also store runtime configuration values for use in a future request.
Since configure only remembers values for the current request, you will
need to store any modified configuration information if you want to
use it in subsequent requests::

    // Store the current configuration in the 'user_1234' key in the 'default' cache.
    Configure::store('user_1234', 'default');

Stored configuration data is persisted in the named cache configuration. See the
:doc:`/core-libraries/caching` documentation for more information on caching.

Restaurando configuraci&oacute;n en tiempo de ejecuci&oacute;n
------------------------------------------------

.. php:staticmethod:: restore($name, $cacheConfig = 'default')

Una vez que se ha guardado la informaci&oacute;n de configuraci&oacute;n, probablemente se necesite
restaurarla para usarla nuevamente. ``Configure::restore()`` hace exactamente eso::

    // Restaura configuraci&oacute;n desde el cach&eacute;.
    Configure::restore('user_1234', 'default');

Cuando se restaura informaci&oacute;n, es importante hacerlo con la misma clave, y la configuraci&oacute;n
de cach&eacute; tal cual fue utilizada para guardarla. La informaci&oacute;n se une sobre la configuraci&oacute;n
en tiempo de ejecuci&oacute;n.

Motores de configuraci&oacute;n
------------------------

CakePHP provee la habilitada para cargar archivos de configuraci&oacute;n de distintas fuentes,
as&iacute; como un sistema "enchufable" para `crear tus propios motores de configuracion
<https://api.cakephp.org/3.x/class-Cake.Core.Configure.ConfigEngineInterface.html>`__.
Los motores de configuraci&oacute;n que vienen con CakePHP son:

* `JsonConfig <https://api.cakephp.org/3.x/class-Cake.Core.Configure.Engine.JsonConfig.html>`__
* `IniConfig <https://api.cakephp.org/3.x/class-Cake.Core.Configure.Engine.IniConfig.html>`__
* `PhpConfig <https://api.cakephp.org/3.x/class-Cake.Core.Configure.Engine.PhpConfig.html>`__

By default your application will use ``PhpConfig``.

Deshabilitar tablas gen&eacute;ricas
=============================

As&iacute; como utilizar clases de tabla gen&eacute;ricas - tambi&eacute;n llamadas auto-tables - cuando se
est&aacute;n creando nuevas aplicaciones y horneando modelos es útil, clases de tablas gen&eacute;ricas pueden
hacer la depuraci&oacute;n dif&iacute;cil en algunos escenarios.

Se puede verificar si una consulta fue emitida desde una tabla gen&eacute;rica v&iacute;a DebugKit, con el
panel SQL. Si aún quedan dudas o problemas para diagnosticar si un issue puede ser
causado por las auto-tablas, se puede tirar una excepci&oacute;n cuando CakePHP usa impl&iacute;citamente
una ``Cake\ORM\Table`` en lugar de la clase concreta con el siguiente c&oacute;digo::

    // In your bootstrap.php
    use Cake\Event\EventManager;
    use Cake\Http\Exception\InternalErrorException;

    $isCakeBakeShellRunning = (PHP_SAPI === 'cli' && isset($argv[1]) && $argv[1] === 'bake');
    if (!$isCakeBakeShellRunning) {
        EventManager::instance()->on('Model.initialize', function($event) {
            $subject = $event->getSubject();
            if (get_class($subject) === 'Cake\ORM\Table') {
                $msg = sprintf(
                    'Missing table class or incorrect alias when registering table class for database table %s.',
                    $subject->getTable());
                throw new InternalErrorException($msg);
            }
        });
    }

.. meta::
    :title lang=en: Configuration
    :keywords lang=en: finished configuration,legacy database,database configuration,value pairs,default connection,optional configuration,example database,php class,configuration database,default database,configuration steps,index database,configuration details,class database,host localhost,inflections,key value,database connection,piece of cake,basic web,auto tables,auto-tables,generic table,class
