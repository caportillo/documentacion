# Tips y cosas que aún no sabemos dónde colocar en el documento

### Null-coalescing operator ?? <a href="#_y7x5sujca0sz" id="_y7x5sujca0sz"></a>

En las últimas versiones de C# y Typescript, y lo más seguro en otros lenguajes, se ha introducido este nuevo operador que nos permite realizar una acción cuando la parte izquierda del operador es null.

Por ejemplo:

```csharp
private static void Display<T>(T a, T backup){
    Console.WriteLine(a ?? backup);
}
```

También existe la versión con asignacion ??=

```csharp
private void Action(int? index){
    index ??= DEFAULT_INDEX
    …
}
```

Esto nos permite escribir código más legible y con muchos menos condicionales que comprueben si la variable es null. Pero tenemos que tener cuidado cuando encadenamos varios seguidos porque es un operador con muy poca prioridad

### JSON y SQL en ejemplos <a href="#_cy09n1s2mbj" id="_cy09n1s2mbj"></a>

```sql
/*
https://docs.microsoft.com/es-es/sql/relational-databases/json/json-data-sql-server?view=sql-server-ver15
Disponible en SQL SERVER y Azure SQL DB
- JSON_VALUE
- JSON_QUERY
- ISJSON
- JSON_MODIFY
- OPENJSON
- FOR JSON PATH
*/
DECLARE @json VARCHAR(MAX) = '{ "id": "testId", "complexObject": { "id": "complexObjectId" } }'
DECLARE @no_json VARCHAR(MAX) = 'example'
-- JSON_VALUE
SELECT '1- ' + JSON_VALUE(@json, '$.id')
SELECT '2- ' + JSON_VALUE(@json, '$.complexObject.id')
-- JSON_QUERY
SELECT '3- ' + JSON_QUERY(@json, '$.complexObject')
-- ISJSON
SELECT '4- '
WHERE ISJSON(@no_json) > 0
-- JSON_MODIFY
SELECT '5- ' + JSON_MODIFY(@json, '$.complexObject.id', 'newId')
SELECT
'6- ' + JSON_MODIFY(@json, '$.id', 'newTestId')
WHERE ISJSON(@json) > 0
AND JSON_VALUE(@json, '$.id') = 'testId'
-- OPENJSON
SELECT *
FROM OPENJSON(@json)
WITH (
Id VARCHAR(MAX) '$.id'
)
-- FOR JSON PATH
SELECT
'7- ' + 'ID' AS Id
FOR JSON PATH
```

### API with custom type <a href="#_78q5pimk1qzm" id="_78q5pimk1qzm"></a>

Usar binario que es superoptimo en tamaño y serialización. Para poder mantener una API con distintos tipos de contenido tenemos varias cabeceras:

Header:

* Accept
* Accept-encoding
* Content-Type
* Content-Enconding

Respuesta:

Codigo 406 en caso de error

En net core es sencillo activar el XML y la compresión. Un poco más difícil en el caso MsgPack, hay que crear un custom formatter, se implementa la interfaz IOutputFormatter

### High availability app service <a href="#_4mt1p3l4p4ku" id="_4mt1p3l4p4ku"></a>

Podemos tenerla dentro de una región, en una zona (eu norte) tenemos 3 edificios (zonas) distintos y podemos activar la redundancia de la zona y te aseguran que van a estar en zona distinta y automáticamente detectara la caída y redirige el tráfico sin necesidad de tener un traffic manager y dos service plan en dos regiones.

### Directiva Cache-Control <a href="#_vjiw45jkcwd4" id="_vjiw45jkcwd4"></a>

TIpos de cache:

* Private: El navegador
* Shared cache: Un proxy o un CDB

Algunas directivas:

* Max-age

Heuristic caching:

### Uso de captchas <a href="#_vww4l9x0p5my" id="_vww4l9x0p5my"></a>

Es una medida de seguridad que permite asegurar que el acceso lo está realizando una persona y no un bot.

Nos permite reducir el spam de envíos de formularios automatizados, pero no es perfecto. También hay que encontrar un equilibrio entre seguridad y usabilidad del usuario.

Hay que usarlos:

* Formularios de registro
* Formularios de solicitud de información
* Generación/Recuperación de contraseñas
* Formularios en general

Hay que tener cuidado con qué herramientas usamos, como Google, que envía información a EE. UU. porque incumplimos la ley de protección de datos.

### Terraform <a href="#_5w9xpkruhn0i" id="_5w9xpkruhn0i"></a>

Es infraestructura desde código, tiene las ventajas:

* De tener todo centralizado
* Con control de versiones
* Tiene ventajas de ser mucho más rápido

Lo primero tenemos que declarar el provider que vamos a usar, en nuestro caso es Azure

También tenemos un fichero State, que sería el estado de la infraestructura actualmente desplegada. Es recomendable tenerlo en un Storage para poder tenerlo sincronizado entre miembros de equipo

#### Buenas prácticas <a href="#_j93x8f5o9r44" id="_j93x8f5o9r44"></a>

**Módulos**

Sería un folder con un fichero main.td y sus \*.tfvars para poder reutilizar los distintos componentes, o servicios, que nos proporciona Azure. Por ejemplo, AppServive, StorageAccount

**Variables**

Nos permite no estar repitiendo la misma información

Tenemos la versión de constantes y variables globales.

Se recomienda tener ficheros \*.tfvars, para tener las variables centralizadas en estos ficheros.

Se recomienda no tener valores por defectos

### WAF <a href="#_3imgo06ekl41" id="_3imgo06ekl41"></a>

### Respuestas eficientes a correos <a href="#_5he8d4mmzbs7" id="_5he8d4mmzbs7"></a>

La comunicación con el cliente es fundamental para proporcionar un buen servicio. Por ello es importante responder eficientemente a los correos. Algunos casos de respuesta eficiente son los siguientes:

* Si un cliente comunica una incidencia o envía un correo de dudas, se debe responder a la mayor brevedad posible indicando que hemos recibido la información y que estamos trabajando en resolver el problema lo antes posible.
* Una vez resuelta una incidencia se debe informar al cliente, pidiendo disculpas por las molestias ocasionadas y explicando el problema con detalle, asegurando que se ha solucionado y no volverá a ocurrir.

### Logic Apps <a href="#_c3kzdr6fh8oi" id="_c3kzdr6fh8oi"></a>

Recurso de Azure que no necesitas programar y permite automatizar las tareas.

### Honeypot <a href="#_b6n40imd5pir" id="_b6n40imd5pir"></a>

Es un sustituto de los captcha.

Tiene dos tipo:

* Orientados a redes: Simula un agujero de seguridad pero entra en una zona controlada mientras recopilamos informacion
* Orientados a spammers: Se crean campos ocultos para que los bots lo rellenen y nos permitan identificarlos y bloquearlos.

Ventajas:

* Son faciles de implementar y adaptables
* Suele funcionar contra bot genericos
* Son invisible para el usuario comparado con los captcha v2

Desventajas:

* Limitan la accesibilidad de los navegadores
* Hay que mantenerlo actualizado manualmente
* Monitorizarlos

### Microsoft Clarity <a href="#_h4rf3cf4md91" id="_h4rf3cf4md91"></a>

Permite ver el uso que hace el usuario con la web, permitiendo ver clicks, heatmaps. Se integra con Google Analytics.

Es gratuito, GDRP, podemos enmascarar los datos.

Muy util para ver los cambios de UX que hacemos para ver si los usuarios lo entienden.

### Herramientas útiles <a href="#_jchzmf3g9zw" id="_jchzmf3g9zw"></a>

### Responsively app <a href="#_4z2cdki1k6nv" id="_4z2cdki1k6nv"></a>

Sirve para ver simultáneamente cómo se ve tu página en múltiples cortes responsive.

URL:

### Tango <a href="#_563itpz563no" id="_563itpz563no"></a>

Te permite hacer explicaciones, paso a paso, de funciones de nuestras web y generar un pdf de documentación.

### Requestly <a href="#_cgpvfid4bx5q" id="_cgpvfid4bx5q"></a>

Es un proxy que nos permite modificar peticiones y respuestas, header y mockear tu server. Es de codigo abierto.

Casos de usos>

* Puedes mockear un endpoint para dar una respuestas
* Reglas para modificar las peticiones para una url en concreto
* Puedes hacer reglas para situaciones especiales, como que no devuelve datos

### Gestión de logs en aplicaciones <a href="#_jcfho8xu8qjy" id="_jcfho8xu8qjy"></a>

En general, conviene tener configurados los logs a ficheros utilizando alguna librería tipo Serilog. En base a las necesidades del proyecto, podría ser necesario también utilizar Application Insights para registrar estadísticas de uso, requests, etc.

A tener en cuenta que Application Insights puede realizar muestreo sobre los eventos y habría que ajustar cada aplicación a las necesidades.

Conviene, en general, separar las interfaces para generar información de Log de información de Auditoría para configurar los requerimientos de ambos sistemas por separado
