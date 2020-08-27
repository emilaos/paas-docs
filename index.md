## Pharmacy As A Service

PAAS hace fullfilment white label de medicamentos permitiéndo la monetización farmacéutica sin tener una farmacia, realizar un envío, procesar un pago ni tener un permiso. **No competimos contra nuestros clientes**, cada checkout es único y el paciente solo puede comprar a través de el.

El proyecto está en desarrollo

### Docs
#### De cero a farmacia en 1, 2, 3.

Simple. Haces un POST request usando tu api key como método de autenticación, el servicio te regresa un checkout único para tu cliente. Cuando tu cliente hace un pedido la utilidad se regitra en tu cuenta como saldo por liberar. Una vez que se realiza el pago los fondos se liberan y puedes retirar tu saldo.

##### ¿Cómo libero mis fondos? ¿Cómo veo mi saldo? ¿Puedo hacer un checkout sin hacer un request?
Si, todo lo puedes hacer desde tu dashboard.

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
  "line_items": <[Object Array]: Obligatorio> [
    ...
    {
      "ean": <String>,
      "quantity": <Integer>
    },
    ...
  ]
```
##### Ejemplo
```
POST "/generateCheckout"
{
  "email": "jane@doe.com",
  "line_items": [
    {
      "ean": "12345678911",
      "quantity": 2
    }
  ]
}
```

### Soporte
Contáctanos. Estamos a un click de distancia.
