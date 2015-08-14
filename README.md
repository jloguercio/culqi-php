# Usando el Botón de Pago Web y PHP

![Status](https://travis-ci.org/culqi/Culqi-PHP.svg)

## Requerimientos

PHP 5.3.3 o posterior, [Mcrypt](http://php.net/manual/es/book.mcrypt.php) y [CURL](http://php.net/manual/es/book.curl.php)

## Instalación

> Puedes importar la [última versión.]()

```php
require 'culqi_src.php';
```

## Configuración

> Para empezar debes de configurar la librería de Culqi:

```php
<?php
Culqi::$llaveSecreta = "llave_secreta";
Culqi::$codigoComercio = "codigo_comercio";
Culqi::$servidorBase = 'https://integ-pago.culqi.com';
?>
```

> Asegurate de reemplazar llave_secreta y codigo_comercio por la llave secreta y el código de comercio que estás integrando.

Estos son los parámetros de configuración:

Parámetro | Descripción
--------- | -----------
llaveSecreta | Llave secreta del comercio
codigoComercio | Código del comercio.
servidorBase | URL de Culqi a la que te conectarás. 
 | `Entorno de Integración la URL es: https://integ-pago.culqi.com` 
 | `Entorno de Producción: la URL es: https://pago.culqi.com`


## Uso

###Creando una venta

Para crear una nueva venta deberás configurar la información de la misma.

```php
<?php
require 'culqi_src.php';

Culqi::$llaveSecreta = "llave_secreta";
Culqi::$codigoComercio = "codigo_comercio";
Culqi::$servidorBase = 'https://integ-pago.culqi.com';

try {

$data = Pago::crearDatospago(array(

Pago::PARAM_NUM_PEDIDO => "tlvh20150727-1",
Pago::PARAM_MONEDA => "PEN",
Pago::PARAM_MONTO => "123",
Pago::PARAM_DESCRIPCION => "123",
Pago::PARAM_COD_PAIS => "123",
Pago::PARAM_CIUDAD => "123",
Pago::PARAM_DIRECCION => "123",
Pago::PARAM_NUM_TEL => "123",
Pago::PARAM_VIGENCIA => "123",

));

$informacionVenta = $data[Pago::PARAM_INFO_VENTA];

echo "Información de la venta: $informacionVenta";

} catch (InvalidParamsException $e) {

echo $e->getMessage()."\n";

}
?>
```

> El parámetro PARAM_INFO_VENTA de la información de respuesta debe de ser usada para configurar el botón de pago de Culqi, explicado a continuación.

### Parámetros obligatorios

Nombre | Parámetro | Descripción | Tipo | Tamaño Máximo
--------- | --------- | ------- | ----------- | -----------
Número de Pedido | PARAM_NUM_PEDIDO | Número de pedido de la venta. | AN | 100 caracteres
Moneda | PARAM_MONEDA | Código de la Moneda de la venta. Ej: Nuevos Soles: PEN , Dólares: USD | N | 3 caracteres
Monto | PARAM_MONTO | Monto de la venta, sin punto decimal Ej: 100.25 sería 10025. | N | 7 caracteres
Descripción | PARAM_DESCRIPCION | Breve descripción del producto o servicio brindado. | AN | 120 caracteres
País | PARAM_COD_PAIS | Código del País del cliente. Ej. Perú : PE | A | 2 caracteres
Ciudad | PARAM_CIUDAD | Ciudad del cliente. | A | 30 caracteres
Dirección | PARAM_DIRECCION | Dirección del cliente. | AN | 80 caracteres
Teléfono | PARAM_NUM_TEL | Número de teléfono del cliente. | N | 20 caracteres

`AN = Alfanumérico` 
`N = Numérico` 

### Parámetros opcionales

Nombre | Parámetro | Descripción | Tipo | Tamaño Máximo
--------- | --------- | ------- | ----------- | -----------
Vigencia | PARAM_VIGENCIA | Cantidad de minutos en los que el cliente puede realizar el pago. | N | 2 caracteres

`N = Numérico` 
`El tiempo de la vigencia es por defecto 10 minutos, para poder cambiarlo contáctate con Culqi.` 


La respuesta que obtendrás será una cadena cifrada que contiene un JSON.

```json
{"info_venta":"dkladkldlakdmdaaldklakd",
 "codigo_comercio":"testc101",
 "nro_pedido":"testc101",
 "codigo_respuesta":"OK",
 "mensaje_respuesta":"Venta Creada",
 "token":"PqHLeGVGBniY7i4XN1N94QIx4MyHHYZhztE"}
```

### Parámetros de la respuesta

Nombre | Parámetro | Descripción | Tipo
--------- | --------- | ------- | -----------
Informacion de Venta | PARAM_INFO_VENTA | La información de la venta que se usa para configurar el botón de pago de Culqi. | AN
Código de Comercio | codigo_comercio | Código del comercio en Culqi. | AN
Número de Pedido | nro_pedido | Número de orden de la venta. | AN
Código de Respuesta | codigo_respuesta | Código de la respuesta. | AN
Mensaje de Respuesta | mensaje_respuesta | Mensaje de respuesta. | AN
Token | token | Token de la transacción. | AN

`AN = Alfanumérico` 

## Integrando el Boton de Pago Web

Para empezar, agrega el siguiente código en JavaScript a tu página web:

`<script type="text/javascript" src="https://integ-pago.culqi.com/culqi.js"></script>`

Usar una copia local no está soportado, y puede resultar en errores visibles por el usuario.

Esta integración te permite crear un botón customizado y pasar un token de Culqi a un callback (Función `Culqi()`) en Javacript. Puedes usar cualquier elemento HTML o evento JavaScript para abrir el formulario de pagos, y es independiente del lenguaje de programación que uses en tu backend.

> Puedes usar Culqi.js de la siguiente manera usando Jquery.

```javascript
//Puedes crear un botón de pago
<button id="btn_pago">Pagar</button>

//Aqui configuramos el botón de pago de Culqi.
<script>

//El código del comercio
checkout.codigo_comercio = "PARAM_COD_COMERCIO";

//La información de la venta
checkout.informacion_venta = "PARAM_INFO_VENTA";


$('#btn_pago').on('click', function(e) {

// Abre el formulario de pago de Culqi.
checkout.abrir();

e.preventDefault();

});

//Esta función es llamada al terminar el proceso de pago.
function culqi (checkout) {

//Aquí recibes la respuesta del formulario de pago.
console.log(checkout.respuesta);

// Cierra el formulario de pago de Culqi.
checkout.cerrar();

};

</script>
```

###Parámetros para configurar el botón de pago

Nombre | Parámetro | Descripción | Tipo
--------- | --------- | ------- | -----------
Código de Comercio | codigo_comercio | Código de comercio en Culqi. | AN
Información Venta | informacion_venta | Información de venta cifrada.  | AN

Es muy importante que entiendas que el atributo `codigo_comercio` se encarga de identificar a tu comercio en la comunicación con los servidores de Culqi.

Y el atributo `informacion_venta` se encarga de enviar la información de la venta al checkout.

Al procesar la transacción, el checkout te enviará como respuesta una cadena de texto, que puedes leer usando la variable `checkout.respuesta` Esta contiene un JSON Cifrado. 

<aside class="error">
Es de suma importancia que envíes la respuesta a tus servidores para descrifrarlo usando la librería de Culqi.</aside>

## Enviando la respuesta tu servidor

Una vez obtengas la respuesta de Culqi en tu página web es necesario que la envíes a tu servidor para descifrarla y poder mostrar al usuario el resultado de la transacción.

## Descrifrando la respuesta

Para descifrar el JSON puedes utilizar la librería de Culqi.

```php
<?php
Culqi.llaveSecreta = "zzmxZlgIJtKKy0F71DMsZPWnPVzow4S90abBScLDIrk=";
Culqi.codigoComercio = "testc101";

//Retorna el JSON Descifrado
return Culqi.decifrar(respuestaCifrada.getRespuesta());

//Codigo del comercio
echo "Código Comercio" . respuesta["codigo_comercio"];

//Número de pedido
echo "Número de pedido" . respuesta["nro_pedido"];

//Código de respuesta
echo "Código Respuesta" . respuesta["codigo_respuesta"];

//Mensaje de respuesta
echo "Mensaje Respuesta" . respuesta["mensaje_respuesta"];

//ID de la Transacción
echo "ID Transacción" . respuesta["id_transaccion"];

//Código de referencia
echo "Código Referencia" . respuesta["codigo_referencia"];

//Código de autorización
echo "Código Autorización" . respuesta["codigo_autorizacion"];

//Marca de la tarjeta
echo "Marca" . respuesta["marca"];

//Emisor de la tarjeta
echo "Emisor" . respuesta["emisor"];

//País de la tarjeta
echo "País Tarjeta" . respuesta["pais_tarjeta"];

}
?>
```
Obtendrás un JSON que contendrá los siguientes parámetros:

Nombre | Parámetro | Descripción | Tipo
--------- | ------- | ----------- | -----------
Código de Comercio | codigo_comercio | Código del comercio en Culqi. | AN
Número de Pedido | nro_pedido | Número de orden de la venta. | AN
Código de Respuesta | codigo_respuesta | Código de la respuesta. | AN
Mensaje de Respuesta | mensaje_respuesta | Mensaje de respuesta. | AN
ID Transacción | id_transaccion | ID de la transacción. | AN
Código Referencia | codigo_referencia | Código de referencia de la transacción. | AN
Código Autorización | codigo_autorizacion | Código de autorización de la transacción. | AN
Marca | marca | Marca de la tarjeta usada para realizar el pago. | AN
Emisor | emisor | Banco emisor de la tarjeta usada para realizar el pago. | AN
País Tarjeta | pais_tarjeta | País de origen de la tarjeta usada para realizar el pago. | AN

`AN = Alfanumérico` 

# Consulta una venta

Para consultar una venta debes de enviar el token de la transacción usando la librería de Culqi.

```php
<?php
require 'culqi_src.php';

Culqi::$llaveSecreta = "zzmxZlgIJtKKy0F71DMsZPWnPVzow4S90abBScLDIrk=";
Culqi::$codigoComercio = "testc101";
Culqi::$servidorBase = 'https://integ-pago.culqi.com';

try {

$data = Pago::consultar("0MXpbwlGjRU9Sr0IwIOqHh1aVJICjGh9KIq");

//Codigo del comercio
echo "Código Comercio" . $data['codigo_comercio'];

//Número de Pedido
echo "Número de pedido" . $data['nro_pedido'];

//Token de la transacción
echo "Token" + $data['token'];

//Estado de la transacción
echo "Estado de la transacción" . $data['estado_transaccion'];

//Código de respuesta
echo "Código de Respuesta" . $data['codigo_respuesta'];

//Mensaje de respuesta
echo "Mensaje de Respuesta" . $data['mensaje_respuesta'];


} catch (InvalidParamsException $e) {

echo $e->getMessage()."\n";

}
?>
```

### Parámetros de envio

Nombre | Parámetro| Descripción | Tipo 
--------- | ----------- | ----------- | -----------
Token | token | El código de la transacción que quieres consultar. | AN


### Parámetros de respuesta

Nombre | Parámetro| Descripción | Tipo 
--------- | --------- | ----------- | -----------
Código de Comercio | codigo_comercio | El código del comercio en Culqi. | AN
Número de Pedido | nro_pedido | El número de orden de tu venta. | AN
Token | token | El código de la transacción. | AN
Estado de Transacción | estado_transaccion | El estado de la transacción. | AN
Código de Respuesta | codigo_respuesta | El código de la respuesta. | AN
Mensaje de Respuesta | mensaje_respuesta | El mensaje de respuesta. | AN

# Anula una venta

Para anular una venta debes de enviar el token de la transacción usando la librería de Culqi.

```php
<?php
require 'culqi_src.php';

Culqi::$llaveSecreta = "zzmxZlgIJtKKy0F71DMsZPWnPVzow4S90abBScLDIrk=";
Culqi::$codigoComercio = "testc101";
Culqi::$servidorBase = 'https://integ-pago.culqi.com';

try {

$data = Pago::anular("0MXpbwlGjRU9Sr0IwIOqHh1aVJICjGh9KIq");

//Codigo del comercio
echo "Código Comercio" . $data['codigo_comercio'];

//Número de Pedido
echo "Número de pedido" . $data['nro_pedido'];

//Token de la transacción
echo "Token" + $data['token'];

//Código de respuesta
echo "Código de Respuesta" . $data['codigo_respuesta'];

//Mensaje de respuesta
echo "Mensaje de Respuesta" . $data['mensaje_respuesta'];


} catch (InvalidParamsException $e) {

echo $e->getMessage()."\n";

}
?>

```

### Parámetros de envio

Parámetro | Tipo | Descripción
--------- | ----------- | -----------
token | AN | El código de la transacción que quieres anular.


### Parámetros de la respuesta

Parámetro | Tipo | Descripción
--------- | ----------- | -----------
codigo_comercio | AN | El código del comercio en Culqi.
nro_pedido | AN | El número de orden de tu venta.
token | AN | El código de la transacción.
codigo_respuesta | AN | El código de la respuesta.
mensaje_respuesta | AN | El mensaje de respuesta.

