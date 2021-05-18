# PAAS | Pharmacy As A Service

## ¿Qué es PAAS?
PAAS hace fulfilment white label de medicamentos, permitiéndo la monetización farmacéutica sin tener una farmacia, realizar un envío, procesar un pago ni tener un permiso. **No competimos contra nuestros clientes**, cada checkout es único y el paciente solo puede comprar a través de el.

## De cero a farmacia en 1, 2, 3.

El sistema es simple. El servicio recibe un POST request usando tu api key como método de autenticación y te regresa un checkout único para tu cliente. Cuando tu cliente hace un pedido la utilidad se registra en tú cuenta como saldo por liberar. Una vez que se realiza el pago los fondos se liberan y puedes retirar tu saldo.

### Ver mis fondos, transferir mi saldo, hacer un checkout sin un post request
Accede a tu dasboard, desde ahi podras hacer todo eso.

### ¡Falta una funcionalidad clave!
No hay problema, vivimos por nuestos clientes ¿Falta desarrollar algo? Lo hacemos :)

## Endpoint, Auth y Headers
El endpoint es `https://us-central1-paas-e9dd6.cloudfunctions.net/`.
Autentica tu request agregando el header `Paas-Api-Key` con tu api key.
El cuerpo de la petición tiene que ser `application/json`.
## Generar un checkout
`POST "/generateCheckout"` con el siguiente objeto.
```
{
  "email": <String: Obligatorio>,
  "lineItems": <[Object Array]: Obligatorio> [
    {
      "ean": <String: Obligatorio>,
      "quantity": <Integer: Obligatorio>
    },
    ...
  ],
  "sendCheckoutByEmail": <Boolean: Opcional>,
  "address": <Object: Opcional> {
    "address1": <String: Obligatorio>,
    "address2": <String>,
    "city": <String: Obligatorio>,
    "province": <String: Obligatorio (Estado)>,
    "zip": <String: Obligatorio>,
    "firstName": <String: Obligatorio>,
    "lastName": <String: Obligatorio>,
    "phone": <String: Obligatorio formato E.164. Ej, +5255...>
   }
}
```
_Opcionalmente_ se puede mandar el objeto de `address` y la bandera de `sendCheckoutByEmail` en el cuerpo de la petición. Para mandar el objeto de dirección algunos de campos son obligatorios y es importante notar que **el campo `province` debe der ser un estado dentro de México**. La bandera `sendCheckoutByEmail` le enviará por correo el checkout al cliente si su valor es `true`.

### Respuesta
El método de crear checkout regresa el siguiente objeto. Cada checkout creado tiene un ID que se relaciona con una orden si el cliente realiza un pedido.
```
{
  "url": <String: Url del checkout>,
  "token": <String: Id único del checkout>,
  "total": <Float: Monto total a pagar antes de envío>,
  "state": <String: Estado del checkout>,
  "email": <String: email del cliente>,
  "emailSentSuccessfully": <Boolean: Solo se regresara si se mando la bandera sendCheckoutByEmail>
}
```

### Ejemplo
```
POST "/generateCheckout"
{
  "email": "jane@doe.com",
  "lineItems": [
    {
      "ean": "1001",
      "quantity": 2
    }
  ]
}
```
### Transacciones de prueba
Puedes simular transacciones con un checkout creado en el entorno de desarrollo, para hacer eso usa estos datos al momento del pago:

|              Campo              |          Valor          |
|-------------------------------|:-----------------------:|
| Nombre                          |      Bogus Gateway      |
| Numero para **transaccion exitosa** |            1            |
| Numero para **error**       |            2            |
| Numero para **excepcion**   |            3            |
| Fecha                           | Cualquiera en el futuro |
| CVV                             |  Cualesquiera 3 digitos |

## Webhooks
Opcionalmente puedes configurar 4 webhooks desde tu dashboard que le avisan a tu backend cuando ocurran eventos relevantes.
* Nueva orden.
* Orden pagada.
* Orden surtida.
* Orden cancelada.

Los webhooks llegan autenticados con el header `Paas-Api-Key` con el api key del ambiente que recibio los eventos y tienen el siguiente cuerpo que los identifica:
* Recuerda, cada checkout que creas genera un *token*. Ese token es un id generado aleatoriamente. Al realizarce una orden, se genera un id numerado.
* El webhook de **nueva orden** incluye en el cuerpo de la peticion el token que genero esta orden y el ID de la orden que servira para identificar los webhooks siguientes.
* El webhook de **orden surtida** incluye los productos surtidos dentro del cuerpo de la peticion.


### Payloads

#### Nueva orden
```
{
  "email": <String: Correo del cliente>,
  "id": <String: Id de la orden>,
  "margin": <Float: Margen que se genero>,
  "status": <String: Status de la orden, puede ser "paid" o "open">,
  "token": <String: Token del carrito>,
  "total": <Float: Precio total de la orden>,
  "url": <String: Url de seguimiento de la orden>
}
```

#### Orden pagada
```
{
  "id": <String: Id de la orden>,
  "margin": <Float: Margen que se genero>,
  "status": "paid"
}
```

#### Orden surtida
```
{
  "id": <String: Id de la orden surtida>,
  "status": "completed",
  "fulfilledItems": <Array: Productos surtidos> {
    "ean": <String: Ean del producto>,
    "name": <String: Nombre del producto>,
    "quantity: <Int: Cantidad surtida>
  }
}
```

#### Orden cancelada
```
{
  "id": <String: Id de la orden cancelada>,
  "status": "canceled"
}
```
## Catalogo de productos
El catalogo de productos puede ser retribuido usando Algolia. Mándanos un correo para compartirte el Api Key.

## Acceder a la info de un checkout o una orden
Podemos acceder a la info de un checkout o una order haciendo una peticion con el token del checkout o el id de la orden.
### Info de un checkout
```
GET "/getCheckout?token={tokenId}"
```
### Info de una orden
```
GET "/getOrder?id={orderId}"
```

## Soporte
Contáctanos en paas.mx. Estamos a un click de distancia.

## Datos para el campo province
Estos son los estados o claves que podemos recibir en el campo `province` del objeto `address` para generar el checkout. Podemos mandar el nombre del estado o la clave.

| Estado              | Clave |
|---------------------|-------|
| Aguascalientes      | AGS   |
| Baja California     | BC    |
| Baja California Sur | BCS   |
| Campeche            | CAMP  |
| Chiapas             | CHIS  |
| Chihuahua           | CHIH  |
| Ciudad de México    | DF    |
| Coahuila            | COAH  |
| Colima              | COL   |
| Durango             | DGO   |
| Guanajuato          | GTO   |
| Guerrero            | GRO   |
| Hidalgo             | HGO   |
| Jalisco             | JAL   |
| México              | MEX   |
| Michoacán           | MICH  |
| Morelos             | MOR   |
| Nayarit             | NAY   |
| Nuevo León          | NL    |
| Oaxaca              | OAX   |
| Puebla              | PUE   |
| Querétaro           | QRO   |
| Quintana Roo        | Q ROO |
| San Luis Potosí     | SLP   |
| Sinaloa             | SIN   |
| Sonora              | SON   |
| Tabasco             | TAB   |
| Tamaulipas          | TAMPS |
| Tlaxcala            | TLAX  |
| Veracruz            | VER   |
| Yucatán             | YUC   |
| Zacatecas           | ZAC   |
