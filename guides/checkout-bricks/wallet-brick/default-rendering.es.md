# Renderizado por defecto

> WARNING
>
> Importante
>
> Para realizar el renderizado de Wallet Brick, primero realice los [pasos de inicialización](/developers/es/docs/checkout-bricks/common-initialization) compartidos entre todos los Bricks. 


## Configurar el Brick

Creae la configuración de inicio de Brick

[[[
```Javascript
const renderWalletBrick = async (bricksBuilder) => {
 const settings = {
   callbacks: {
     onReady: () => {
     /*
      Callback llamado cuando Brick está listo.
      Aquí puedes ocultar cargamentos de su sitio, por ejemplo.
     */
   },
   onSubmit: (formData) => {
     // callback llamado al hacer clic en Wallet Brick
     // esto es posible porque el ladrillo es un botón
     // en este momento del envío, debe crear la preferencia
     const yourRequestBodyHere = {
       items: [
         {
           id: '202809963',
           title: 'Dummy title',
           description: 'Dummy description',
           quantity: 1,
           unit_price: 10,
         },
       ],
       purpose: 'wallet_purchase',
     };
     return new Promise((resolve, reject) => {
       fetch('/create_preference', {
         method: 'POST',
         headers: {
           'Content-Type': 'application/json',
         },
           body: JSON.stringify(formData),
         })
           .then((response) => response.json())
           .then((response) => {
           // resolver la promesa con el ID de la preferencia
           resolve(response.preference_id);
         })
         .catch((error) => {
           // manejar la respuesta de error al intentar crear preferencia
           reject();
         });
     });
   },
 },
};
window.walletBrickController = await bricksBuilder.create(
   'wallet',
   'walletBrick_container',
   settings,
  );
 
};
renderWalletBrick(bricksBuilder);
```
```react-jsx
const onSubmit = async (formData) => {
 // callback llamado al hacer clic en Wallet Brick
 // esto es posible porque el ladrillo es un botón
 // en este momento del envío, debe crear la preferencia
 const yourRequestBodyHere = {
   items: [
     {
       id: '202809963',
       title: 'Dummy title',
       description: 'Dummy description',
       quantity: 1,
       unit_price: 10,
     },
   ],
   purpose: 'wallet_purchase',
 };
 return new Promise((resolve, reject) => {
   fetch('/create_preference', {
     method: 'POST',
     headers: {
       'Content-Type': 'application/json',
     },
     body: JSON.stringify(yourRequestBodyHere),
   })
     .then((response) => response.json())
     .then((response) => {
       // resolver la promesa con el ID de la preferencia
       resolve(response.preference_id);
     })
     .catch((error) => {
       // manejar la respuesta de error al intentar crear preferencia
       reject();
     });
 });
};


const onError = async (error) => {
 // callback llamado para todos los casos de error de Brick
 console.log(error);
};


const onReady = async () => {
 /*
   Callback llamado cuando Brick está listo.
   Aquí puedes ocultar cargamentos de su sitio, por ejemplo.
 */
};
```
]]]

> WARNING
> 
> Atención
>
> Si es necesario desmontar y volver a montar un Brick, se recomienda destruir la instancia actual y generar una nueva. Para hacerlo, usa el método *unmount* disponible en el *controller* de Brick, en este caso: `window.paymentBrickController.unmount()`.

Este flujo de creación de [preferencia en onSubmit](/developers/es/docs/checkout-bricks/wallet-brick/configure-integration/preference-onsubmit) está diseñado para vendedores que tienen flujos de one clic, si lo desea, también puede enviar Preferencia en el inicio. Ver más información en la sección [Preferencia en el inicio](/developers/es/docs/checkout-bricks/wallet-brick/additional-customization/preference-startup).

## Renderizar el Brick

Una vez creadas las configuraciones, ingrese el código a continuación.

> NOTE
>
> Importante
>
> El id 'walletBrick_container' de la div HTML abajo debe corresponder que el valor enviado en el metodo create() de la etapa anterior.

[[[
```html
<div id="walletBrick_container"></div>
```
```react-jsx
import { Wallet } from '@mercadopago/sdk-react';


<Wallet
   onSubmit={onSubmit}
   onReady={onReady}
   onError={onError}
/>
```
]]]

El resultado de renderizar el Brick debería parecerse a la imagen de abajo.

<center>

![wallet-brick-render](checkout-bricks/wallet-brick-render-es.png)

</center>

> Si desea cambiar el texto del Brick, consulte la sección [Cambiar textos.](/developers/es/docs/checkout-bricks/wallet-brick/additional-customization/change-texts)

## Habilitar pago con Mercado pago

Para utilizar un método de pago (paymentMethods) del tipo "mercadoPago", se debe enviar una preferencia durante la inicialización del Brick, reemplazando el valor <PREFERENCE_ID> por el ID de la preferencia creada.

Para crear una preferencia en su backend, agrega el [SDK de Mercado Pago](/developers/es/docs/sdks-library/landing) y las [credenciales](/developers/es/guides/additional-content/credentials/credentials) necesarias a tu proyecto para habilitar el uso de preferencias:

[[[
```php
<?php
// SDK de Mercado Pago
require __DIR__ .  '/vendor/autoload.php';
// Agrega credenciales
MercadoPago\SDK::setAccessToken('PROD_ACCESS_TOKEN');
?>
```
```node
// SDK de Mercado Pago
const mercadopago = require("mercadopago");
// Agrega credenciales
mercadopago.configure({
  access_token: "PROD_ACCESS_TOKEN",
});
```
```java
// SDK de Mercado Pago
import com.mercadopago.MercadoPagoConfig;
// Agrega credenciales
MercadoPagoConfig.setAccessToken("PROD_ACCESS_TOKEN");
```
```ruby
# SDK de Mercado Pago
require 'mercadopago'
# Agrega credenciales
sdk = Mercadopago::SDK.new('PROD_ACCESS_TOKEN')
```
```csharp
// SDK de Mercado Pago
 using MercadoPago.Config;
 // Agrega credenciales
MercadoPagoConfig.AccessToken = "PROD_ACCESS_TOKEN";
```
```python
# SDK de Mercado Pago
import mercadopago
# Agrega credenciales
sdk = mercadopago.SDK("PROD_ACCESS_TOKEN")
```
]]]

----[mla, mlb, mlm]----
Luego establezca la preferencia de acuerdo a su producto o servicio. 

Los ejemplos de código a continuación establecen el **purpose de la preferencia** en `wallet_purchase`, pero también es posible establecerlo en `onboarding_credits`. Entienda la diferencia entre los dos:

* **wallet_purchase**: el usuario debe iniciar sesión cuando es redirigido a su cuenta de Mercado Pago.
* **onboarding_credits**: luego de iniciar sesión, el usuario verá la opción de pago de crédito preseleccionada en su cuenta de Mercado Pago.

------------
----[mlu, mlc, mco, mpe]----

Luego establezca la preferencia de acuerdo a su producto o servicio. 

Los ejemplos de código a continuación establecen el **purpose de la preferencia** en `wallet_purchase`, donde el usuario debe iniciar sesión cuando es redirigido a su cuenta de Mercado Pago.

------------

[[[
```php
<?php

// Crear un objeto de preferencia
$preference = new MercadoPago\Preference();

// Crear un elemento en la preferencia
$item = new MercadoPago\Item();
$item->title = 'Meu produto';
$item->quantity = 1;
$item->unit_price = 75.56;
$preference->items = array($item);

// el $preference->purpose = 'wallet_purchase'; solo permite pagos registrados
// para permitir pagos de guests, puede omitir esta propiedad
$preference->purpose = 'wallet_purchase';
$preference->save();
?>
```
```node
// Crear un objeto de preferencia
let preference = {
  // el "purpose": "wallet_purchase" solo permite pagos registrados
  // para permitir pagos de guests puede omitir esta propiedad
  "purpose": "wallet_purchase",
  "items": [
    {
      "id": "item-ID-1234",
      "title": "Meu produto",
      "quantity": 1,
      "unit_price": 75.76
    }
  ]
};

mercadopago.preferences.create(preference)
  .then(function (response) {
    // Este valor es el ID de preferencia que se enviará al ladrillo al inicio
    const preferenceId = response.body.id;
  }).catch(function (error) {
    console.log(error);
  });
```
```java
// Crear un objeto de preferencia
PreferenceClient client = new PreferenceClient();

// Crear un elemento en la preferencia
List<PreferenceItemRequest> items = new ArrayList<>();
PreferenceItemRequest item =
   PreferenceItemRequest.builder()
       .title("Meu produto")
       .quantity(1)
       .unitPrice(new BigDecimal("100"))
       .build();
items.add(item);

PreferenceRequest request = PreferenceRequest.builder()
  // el .purpose('wallet_purchase') solo permite pagos registrados
  // para permitir pagos de guest, puede omitir esta línea
  .purpose('wallet_purchase')
  .items(items).build();

client.create(request);
```
```ruby
# Crear un objeto de preferencia
preference_data = {
  # el purpose: 'wallet_purchase', solo permite pagos registrados
  # para permitir pagos de guests, puede omitir esta propiedad
  purpose: 'wallet_purchase',
  items: [
    {
      title: 'Meu produto',
      unit_price: 75.56,
      quantity: 1
    }
  ]
}
preference_response = sdk.preference.create(preference_data)
preference = preference_response[:response]

# Este valor es el ID de preferencia que usará en el HTML en el inicio del Brick
@preference_id = preference['id']
```
```csharp
// Crear el objeto de request de preferencia
var request = new PreferenceRequest
{
  // el Purpose = 'wallet_purchase', solo permite pagos registrados
  // para permitir pagos de invitados, puede omitir esta propiedad
    Purpose = 'wallet_purchase',
    Items = new List<PreferenceItemRequest>
    {
        new PreferenceItemRequest
        {
            Title = "Meu produto",
            Quantity = 1,
            CurrencyId = "BRL",
            UnitPrice = 75.56m,
        },
    },
};

// Crea la preferencia usando el cliente
var client = new PreferenceClient();
Preference preference = await client.CreateAsync(request);
```
```python
# Crea un elemento en la preferencia
preference_data = {
  # el "purpose": "wallet_purchase", solo permite pagos registrados
  # para permitir pagos de invitados, puede omitir esta propiedad
    "purpose": "wallet_purchase",
    "items": [
        {
            "title": "Mi elemento",
            "quantity": 1,
            "unit_price": 75.76
        }
    ]
}

preference_response = sdk.preference().create(preference_data)
preference = preference_response["response"]
```
```curl
curl -X POST \
'https://api.mercadopago.com/checkout/preferences' \
-H 'Content-Type: application/json' \
-H 'cache-control: no-cache' \
-H 'Authorization: Bearer **PROD_ACCESS_TOKEN**' \
-d '{
  "purpose": "wallet_purchase",
  "items": [
      {
          "title": "Mi producto",
          "quantity": 1,
          "unit_price": 75.76
      }
  ]
}'
```
]]]

> NOTE
>
> Importante
>
> Para más detalles sobre cómo configurarlo, acceda a la sección [Preferencias.](/developers/es/docs/checkout-bricks/wallet-brick/additional-customization/preferences)<br/></br>
> <br/></br>
> Considera que cuando un usuario elige realizar un pago a través de la Billetera de Mercado Pago, será redirigido a la página de Mercado Pago para completar el pago. Por lo tanto, es necesario configurar `back_urls` si desea volver a su sitio al finalizar el pago. Para obtener más información, visite la sección [Redirigir al comprador a su sitio web.](/developers/es/docs/checkout-bricks/wallet-brick/additional-customization/preferences#bookmark_redirigir_al_comprador_a_tu_sitio_web)

