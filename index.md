## Pharmacy As A Service

PAAS hace fulfilment white label de medicamentos, permitiéndo la monetización farmacéutica sin tener una farmacia, realizar un envío, procesar un pago ni tener un permiso. **No competimos contra nuestros clientes**, cada checkout es único y el paciente solo puede comprar a través de el.

### Docs
#### De cero a farmacia en 1, 2, 3.

Simple. Haces un POST request usando tu api key como método de autenticación, el servicio te regresa un checkout único para tu cliente. Cuando tu cliente hace un pedido la utilidad se regitra en tu cuenta como saldo por liberar. Una vez que se realiza el pago los fondos se liberan y puedes retirar tu saldo.

##### Ver mis fondos, transferir mi saldo, hacer un checkout sin un post request
Accede a tu dasboard, desde ahi podras hacer todo eso.

##### ¡Falta una funcionalidad clave!
No hay problema, vivimos por nuestos clientes ¿Falta desarrollar algo? Lo hacemos :)

#### Endpoint, Auth y Headers
El endpoint es `https://us-central1-paas-e9dd6.cloudfunctions.net/`.
Autentica tu request agregando el header `Paas-Api-Key` con tu api key.
El cuerpo de la petición tiene que ser `application/json`.
#### Generar un checkout
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
  ]
}
```
Opcionalmente, se puede mandar el objeto de dirección en el cuerpo de la petición. Que aunque de momento no es funcional, lo será en unas semanas. Para mandar el objeto address, todos sus campos son obligatorios.
```
"address": {
  "address1": <String>,
  "address2": <String>,
  "city": <String>,
  "company": <String>,
  "country": <String>,
  "firstName": <String>,
  "lastName": <String>,
  "phone": <String>,
  "province": <String>,
  "zip": <String>
 }
```
##### Ejemplo
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
#### Transacciones de prueba
Puedes simular transacciones con un checkout creado en el entorno de desarrollo, para hacer eso usa estos datos al momento del pago:

|              Campo              |          Valor          |
|-------------------------------|:-----------------------:|
| Nombre                          |      Bogus Gateway      |
| Numero para **transaccion exitosa** |            1            |
| Numero para **error**       |            2            |
| Numero para **excepcion**   |            3            |
| Fecha                           | Cualquiera en el futuro |
| CVV                             |  Cualesquiera 3 digitos |

### Soporte
Contáctanos. Estamos a un click de distancia.
