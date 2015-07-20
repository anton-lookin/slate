---
title: NimbleCommerce API Reference

language_tabs:
  - shell
  - objective_c
  - java

toc_footers:
  - <a href='http://www.nimblecommerce.com'>Copyright © 2015 Nimblecommerce</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the NimbleCommerce API! You can use our API to access NimbleCommerce API endpoints.

We have language bindings in Shell, Objective-C for iOS, and Java for Android! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Authorization

NimbleCommerce SDK requires user authorization for a number of API calls. SDK has implemented authorization scheme which is ready to use in your applications.


**iOS**

`NCAuthorizationManager usage is recommended way to embed user authorization in your application`

`NCAuthorizationManager` will automatically handle all authorization routines credentials saving, tokens caching and renewal.

`Authorization token and user credentials securely saved in keychain`

NimbleCommerce SDK gives ability to build your own authorization handling. You should use `NCAuthorization` loader to get you authorization token.

To handle result you should register for notifications defined in `NCAuthorizationManager.h`. 
`NCAuthorizationManagerAuthorizationCompleted` - notification fired when user authorization succeed.
`NCAuthorizationManagerAuthorizationFailed` - notification fired when user authorization failed for some reason, error code can be found in notification's userInfo by 'kNCAuthorizationErrorCode' key.
`NCAuthorizationManagerDeauthorizationCompleted` - notification fired when user deautorization succeed.

**Android**

`AuthManager` usage is recommended way to embed user authorization in your application`

`NCAuthorizationManager` will automatically handle all authorization routines credentials saving, tokens caching and renewal.

`Authorization token and user credentials securely saved in secured preferences`

Be sure to call 'authorize' method inside async context, `authorize()` is sync method and can throw number of exception, you should handle this manually.
`AuthManager` fires couple of local notifications, so you could catch result in different parts of your application.
`LOGIN_EVENT` - notification fired when user authorization succeed.
`LOGOUT_EVENT` - notification fired when user deauthorization succeed.
`SIGN_UP_EVENT` - notification fired when user registration succeed.

<aside class="notice">
Make sure to replace `Authorization Token` with your Authorization Token for all requests that require authorization. If you use managers than this handlings will be done automatically.
</aside>

## Authorization with credentials

Received `Authorization Token` has limited lifetime but long enough to not mess with reauthorization while application running. 
It is recommended to update token when your application start.

<aside class="notice">
Be sure to do all the secure requests over `https` protocol.
</aside>

> Performs user authorization by login and password.

```objective_c
#import <NimbleCommerceSDK/NCAuthorizationManager.h>

[[NCAuthorizationManager sharedInstance] authorizeWithEmail:email password:password];
```

```java
import gb.android.sdk.Nmb;
import gb.android.sdk.managers.AuthManager;

Nmb.getAuthManager().authorize(email, password);
```

```shell
curl \
	-X POST \
	-d "email={USER_EMAIL}&password={USER_PASSWORD}" \
	-H "x-nimblecommerce-api-version: 6" \
	-H "Accept: application/json; charset=UTF-8" \
	https://stage.nimblebuy.com/api/authorize?merchantId=78
```

## Authorization with Facebook

You should setup application for facebook sign in, receive user data and fill `facebookData` bean.
<aside class="notice">
Note, email is mandatory field, so you should setup permission on your facebook application.
</aside>

> Performs user authorization with Facebook account.

```objective_c
#import <NimbleCommerceSDK/NCAuthorizationManager.h>

[[NCAuthorizationManager sharedInstance] authorizeWithFacebook:facebookData];
```

```java
import gb.android.sdk.Nmb;
import gb.android.sdk.managers.AuthManager;

Nmb.getAuthManager().authorize(facebookUserBean, mFacebook.getSession().getAccessToken(), birthDate);
```

# Shopping Cart

Shopping Cart API methods use hybrid parameters scheme, shopping cart related parameters are passed in request body as JSON.
NimbleCommerce parameters such as `merchantId`, `localeId`, `securityKey` are passed in URL.
Shopping Cart API uses cookie to save shopping cart basic information.

<aside class="notice">
You should handle shopping cart cookie manually, if you using Shopping Cart API directly.
You can access cookie by name `sc_{MERCHANT_ID}`
</aside>

## Structure

Shopping cart cookie is stored as json and has strict format.
Cookie value saved as percent escaped UTF8 string enclosed with quotes.
Shopping cart can be accessed by decoding cookie value, removing enclosing quotes and parsing json string.
Cookie saves basic shopping cart information about deals that added to cart.

Cookie deal parameters:

Key | Type | Description
--------- | --------- | -----------
id | number | deal identifier.
cId | number | category (city) identifier.
o | array | array of deal offers.

Cookie offer parameters:

Key | Type | Description
--------- | --------- | -----------
id | number | offer identifier
q | number | offer selected quantity
a | array | array of selected offer attributes (map of attributeId -> optionId)
 
<aside class="notice">
Decoded cookie value example: \"[{"id":12312,"cId":223,"gifted":false,"o":[{"id":13141,"q":1}]},{"id":13510,"cId":223,"o":[{"id":14508,"q":2,"a":[{"2316":4753,"2318":4759,"2317":4758},{"2316":4754,"2318":4760,"2317":4757}]},{"id":14509,"q":1}]}]\"
</aside>

## Add Item

> Adding item shopping cart

```objective_c
	NCShoppingCartItem *item = [[NCShoppingCartItem alloc] init];
	item.dealId = @2296932;
	item.categoryId = @25221;
	
	NCShoppingCart *cart = [NCShoppingCart cart];
	[cart addItem:item handler:^(NSError *error) {
		if (error) {
			// Error
		} else {
			// Success
		}
	}];
```

```java
	ShoppingCartItem item = new ShoppingCartItem().categoryId(25221).dealId(2296932);
	ShoppingCart.getInstance().addItem(item, new ShoppingCart.ResultHandler() {
		@Override
		public void onSuccess() {
			
		}
		@Override
		public void onFailure(NCResponse response) {
		
		}
	}, getActivity());
```

```shell
curl \
	-X PUT \
	-d "{\"cityId\":25221,\"dealId\":2296932,\"offers\":[]}" \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Accept: application/json; charset=UTF-8" \
	-H "Content-Type: application/json; charset=UTF-8" \
	https://stage.nimblebuy.com/api/shoppingCart?merchantId=78
```

Adds item to shopping cart. Parameters sent as json string in request body.

<aside class="notice">
You should use proper content type header for requests with json body `application/json; charset=UTF-8`.
</aside>

Key | Type | Description
--------- | --------- | -----------
dealId | number | deal identifier
cityId | number | category (city) identifier
offers | array | empty array, will return default offer quantities in result cookie

## Remove Item

> Removes item from shopping cart

```objective_c
	[[NCShoppingCart cart] removeItem:@2296932];
```

```java
	ShoppingCart.getInstance().removeItem(2296932);
```

Removing shopping cart item does not require network calls. 
You should remove deal object from cookie and sync your UI code.

## Load Details

> Loads shopping cart details.

```objective_c
	[[NCShoppingCart cart] load:^(NSError *error) {
		if (error) {
			// Handle error
		} else {
			// Handle results and update UI
		}
	}];
```

```java
	shoppingCart.load(new ShoppingCart.ResultHandler() {
		@Override
		public void onSuccess() {
			// Handle results and update UI
		}
		@Override
		public void onFailure(NCResponse response) {
			// Handle error
		}, this);
```

```shell
curl \
	-X GET \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Accept: application/json; charset=UTF-8" \
	https://stage.nimblebuy.com/api/shoppingCart?merchantId=78
```

> Shopping cart details json

```json
{
  "dealsOrder": [
    {
      "dealId": 2296932,
      "cityId": 25221,
      "isGift": false,
      "offers": [
        {
          "offerId": 2970562,
          "count": 1
        }
      ]
    }
  ],
  "deals": [
    {
      "id": 2296932,
      "shortTitle": "",
      "title": "Product Group Offer inc tax",
      "endDate": "07/31/2015",
      "type": "PRODUCT",
      "subMerchantId": 8,
      "quantityLimit": 5,
      "quantityGiftLimit": 5,
      "status": "LIVE",
      "image": {
        "title": "Product Group Offer inc tax",
        "href": "https://s3.amazonaws.com/shop_note_stage/nimblebuy/product-group-offer-inc-tax-4302642-regular.jpg"
      },
      "offers": [
        {
          "id": 2970562,
          "title": "Product Group Offer inc tax",
          "shortTitle": "",
          "quantity": 5,
          "giftQuantity": 5,
          "price": 50.0,
          "attributes": [
            
          ],
          "shipping": {
            "type": "ITEM",
            "methods": [
              {
                "id": 29312,
                "name": "FedEx-Next Day",
                "time": "1 Day",
                "price": 20.0
              },
              {
                "id": 29322,
                "name": "FedEx-Ground",
                "time": "5-7 Days",
                "price": 5.0
              }
            ]
          },
          "isLive": true
        }
      ],
      "options": {
        "hasTax": false,
        "hasGifting": true,
        "hasCredits": true,
        "hasPromoCode": true,
        "hasGuestCheckout": true,
        "hasShipping": true
      },
      "merchants": [
        {
          "id": 8,
          "name": "dave's photo"
        }
      ],
      "taxSettings": {
        "id": 6002,
        "gbId": 2296932,
        "applyToShipping": true,
        "applyToCredits": true,
        "applyToPromoCode": true,
        "applyToGift": true,
        "includeToPrice": true,
        "grossType": "TOTAL_GROSS",
        "taxKeeper": "SUB_MERCHANT",
        "addressToUse": "ORIGIN_ADDRESS",
        "originAddress": {
          "address": "2829 mission st",
          "city": "San Francisco",
          "state": "California",
          "zip": "94110",
          "fullAddress": "2829 mission st",
          "joinedAddress": "2829 mission st"
        },
        "taxRuleSetId": 1222,
        "applyTaxesNotNeededForSomeVirtualMoney": false
      }
    }
  ],
  "options": {
    "hasDiscountProgram": true,
    "hasCredits": false,
    "hasPromoCode": true,
    "hasGiftCard": false,
    "hasGiftCardPin": false,
    "hasAddressSave": false,
    "hasAddressVerification": true,
    "hasCardlinking": false,
    "hasRequiredCardlinking": false,
    "hasRaf": false,
    "hasShipping": true,
    "hasGuestCheckout": true
  },
  "settings": {
    "localeShortName": "en",
    "priceFormat": "$####.##",
    "datePattern": "MM/dd/yyyy",
    "timePattern": "HH:mm:ss",
    "currentCountry": {
      "id": 2,
      "name": "US",
      "code": "US"
    },
    "msToExpire": 86400000
  },
  "billing": {
    "type": "OnlineMart",
    "has3dSecure": false,
    "hasRedirect": false,
    "has1ClickBuy": false,
    "secondaryTypes": [
      
    ],
    "creditCardTypes": [
      {
        "name": "Visa",
        "type": "visa",
        "pattern": "^4[0-9]{12}(?:[0-9]{3})?$"
      },
      {
        "name": "Master Card",
        "type": "master_card",
        "pattern": "^5[1-5][0-9]{14}$"
      },
      {
        "name": "Discover",
        "type": "discover",
        "pattern": "^6(011|5[0-9]{2})[0-9]{12}$"
      },
      {
        "name": "Amex",
        "type": "amex",
        "pattern": "^3[47][0-9]{13}$"
      }
    ],
    "creditCardFee": 0.0
  }
}
```

Shopping cart details loading requires cookie to be set in request. 
As result you'll get details to show shopping cart UI to user and proceed with checkout process.
Results can be accesses as properties in Shoppint Cart singletones on iOS/Android SDKs, this classes have identical to JSON structure.

JSON structure description:

Key | Type | Description
--------- | --------- | -----------
dealsOrder | object | order in JSON format, will represent cookie value
deals | array | list of deal details
options | object | set of shopping cart options, flags that presents different NimbleCommerce features
settings | object | overall shopping cart settings
billing | object | shopping cart billing settings

## Calculations

### Taxes

### Promo codes

> Loads promo code information

```objective_c

	// NOTE, You should save strong refernce to the loaders
	self.promoCodeLoader = [[NCPromoCodeValidate alloc] init];
	self.promoCodeLoader.delegate = self;
	[self.promoCodeLoader validatePromoCode:@"111111" deals:@[@2296932]];
	
	// Implement delegated methods and process result.
	-(void) promoCodeValidationSucceed:(NCPromoCodeValidate *)dataSource {
	}
	
	-(void) promoCodeValidationFailed:(NCPromoCodeValidate *)dataSource withError:(NSError *)error {
	}
```

```java
	PromoCodeAmount.ResultHandler resultHandler = new PromoCodeAmount.ResultHandler() {
		@Override
		public void onSuccess(PromoCodeAmount.Response response) {
			// Handle success
			}
			@Override
			public void onError(PromoCodeAmount.Response response) {
			// Handler error
			}
		};
		new PromoCodeAmount(this)
				.dealIds(Lists.newArrayList(2296932))
				.promoCode("111111")
				.resultHandler(resultHandler)
				.execute();
		}
```

```shell
curl \
	-X GET \
	-d "{\"cityId\":25221,\"dealId\":2296932,\"offers\":[]}" \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Accept: application/json; charset=UTF-8" \
	-H "Content-Type: application/json; charset=UTF-8" \
	https://stage.nimblebuy.com/api/shoppingCart?merchantId=78
```

> Promo code details result JSON

```json
{
  "amount": 1234.0,
  "type": "AMOUNT",
  "dealIds": [
    2296932
  ]
}
```

Loads promo code value for applicable deals. Will return array with deal identifiers that can apply promo code.

Call parameters:

Parameter | Type | Description
--------- | --------- | -----------
number | string | promo code number
dealIds | string | deals identifiers comma separated string

Result values:

Key | Type | Description
--------- | --------- | -----------
amount | number | promo code amount, can represent percent value
type | string | promo code type, `AMOUNT` or `PERCENT`
dealIds | array | list of applicable deals

### Gift cards



## Checkout

