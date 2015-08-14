---
title: NimbleCommerce API Reference

language_tabs:
  - shell: Shell
  - objective_c: Objective-C
  - java: Java

toc_footers:
  - <a href='http://www.nimblecommerce.com'>Copyright © 2015 Nimblecommerce</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors
  - api_changes
  - ios_changes
  - android_changes

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

## Authorization with Credentials

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

*Request body JSON parameters:*

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

*Result JSON values:*

Key | Type | Description
--------- | --------- | -----------
dealsOrder | object | order in JSON format, will represent cookie value
deals | array | list of deal details
options | object | set of shopping cart options, flags that presents different NimbleCommerce features
settings | object | overall shopping cart settings
billing | object | shopping cart billing settings

## Promo codes

> Loads promo code information

```objective_c

	// NOTE, You should keep strong reference to the loaders
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
	-X POST \
	-d "{\"number\":111111,\"dealIds\":"2296932"}" \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Accept: application/json; charset=UTF-8" \
	-H "Content-Type: application/json; charset=UTF-8" \
	https://stage.nimblebuy.com/api/shoppingCart/calculate?merchantId=78\&type=PROMO_CODE
```

> Result JSON

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

*Request body JSON parameters:*

Parameter | Type | Description
--------- | --------- | -----------
number | string | promo code number
dealIds | string | deals identifiers comma separated string

*Result JSON values:*

Key | Type | Description
--------- | --------- | -----------
amount | number | promo code amount, can represent percent value
type | string | promo code type, `AMOUNT` or `PERCENT`
dealIds | array | list of applicable deals

## Gift cards

> Loads gift card information

```shell
curl \
	-X POST \
	-d "{\"number\":62035,\"pin\":\"\"}" \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Authorization: {AUTHORIZATION_TOKEN}"
	-H "Accept: application/json; charset=UTF-8" \
	-H "Content-Type: application/json; charset=UTF-8" \
	https://stage.nimblebuy.com/api/shoppingCart/calculate?merchantId=78\&type=GIFT_CARD
```

```objective_c

	// NOTE, You should keep strong references to the loaders
	self.giftCardValidator = [[NCGiftCardValidate alloc] init];
	self.giftCardValidator.delegate = self;
	[self.giftCardValidator validateGiftCard:@"62035" pinCode:@""];
	
	// Implement delegated methods and process result.
	-(void) giftCardValidationSucceed:(NCGiftCardValidate *)dataSource {
	}
	-(void) giftCardValidateFailed:(NCGiftCardValidate *)dataSource withError:(NSError *)error {
	}
```

```java
	CheckGiftCard.ResultHandler resultHandler = new CheckGiftCard.ResultHandler() {
		@Override
		public void onSuccess(GiftCard giftCard) {
		// Handle success	
		}
		@Override
		public void onError(String errorMessage) {
		// Handle error
		}
		new CheckGiftCard(getActivity())
				.cardNumber("62035")
				.cardPin("")
				.resultHandler(resultHandler)
				.execute();
```

> Result JSON

```json
{
  "amount": 10.0
}
```

Checks gift card balance. Returns amount of available credits on gift card.

<aside class="notice">
Authorization required for this call, you should put valid `Authorization Token` into request headers.
</aside>

*Request body JSON parameters:*

Parameter | Type | Description
--------- | --------- | -----------
number | string | gift card number
pin | string | gift card pin code

*Result JSON values:*

Key | Type | Description
--------- | --------- | -----------
amount | number | gift card available balance

## Taxes

> Loads tax amounts for shopping cart

```shell
curl \
	-X POST \
	-d '{"shippingAddress":{"address":"sdflsjdklf","address2":"sjdfkjsdkfj","firstName":"laksdjflksdjf","city":"sdjfksdjfk","phone":"28348902384","zip":"90210","countryCode":"US","state":"California","lastName":"lksdjfklsdjfkl"},"dealsOrder":[{"dealId":2296952,"cityId":25221,"offers":[{"offerId":2970592,"count":1,"shippingMethodId":29352}]}]}' \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Authorization: {AUTHORIZATION_TOKEN}" \
	-H "Accept: application/json; charset=UTF-8" \
	-H "Content-Type: application/json; charset=UTF-8" \
	https://stage.nimblebuy.com/api/shoppingCart/calculateTaxAmount?merchantId=78
```

```objective_c
	NCShoppingCartOrderDiscounts *discounts = [[NCShoppingCartOrderDiscounts alloc] init];
	discounts.promoCode = {PROMO_CODE};
	discounts.giftCards = {GIFT_CARDS};
	discounts.credits = {USED_CREDITS};
	
	NCShoppingCartOrderInfo *orderInfo = [[NCShoppingCartOrderInfo alloc] init];
	orderInfo.discounts = discounts;
	orderInfo.shippingAddress = {SHIPPING_ADDRESS};
	
	if (isCardless) {
		if (paymentAccount) {
			orderInfo.paymentInfo = [NCShoppingCartPaymentInfo paymentInfoFromPaymentAccount:paymentAccount];
		} else if (billingAddress) {
			orderInfo.billingAddress = billingAddress;
		}
	} else {
		if (hasBillingTaxes) {
			orderInfo.taxAddress = taxAddress;
		}
	}
	
	[[NCShoppingCart cart] loadTaxAmountsWithOrderInfo:orderInfo
											   handler:^(NSError *error, NSArray *taxAmounts) {
												   // Handle results
											   }];
```

```java
	ShoppingCart.OrderInfo orderInfo = new ShoppingCart.OrderInfo();
	orderInfo.discounts().promoCode(promoCode);
	orderInfo.discounts().giftCards(giftCards());
	orderInfo.discounts().credits(creditsUsed());
	if (isCardless) {
		orderInfo.taxAddress(taxAddress);
	} else {
		if (null != paymentInfo()) {
			orderInfo.paymentInfo(ShoppingCart.OrderPaymentInfo.newInstance(paymentInfo()));
			if (null == paymentInfo.paymentAccountId()) {
				orderInfo.billingAddress(paymentInfo.billingAddress());
			}
		}
	}
	orderInfo.shippingAddress(shippingAddress);
	ShoppingCart.getInstance().loadTaxAmounts(orderInfo, new ShoppingCart.ResultHandler() {
		@Override
		public void onSuccess() {
			// Handle success
		}

		@Override
		public void onFailure(NCResponse response) {
			// Handle error
		}, getActivity());
```

> Result JSON

```json
{
  "offersTaxData": [
    {
      "offerId": 2970592,
      "gbId": 2296952,
      "amount": 6.30,
      "includedToPrice": false,
      "taxEnabled": true,
      "canPayTaxesWithCredits": true,
      "canPayTaxesWithPromoCode": true,
      "canPayTaxesWithGiftCards": true
    }
  ]
}
```

*Request body JSON parameters:*

Parameter | Type | Description
--------- | --------- | -----------
dealsOrder | array | array of deals in shopping cart
discounts | object | map of used discounts for shopping cart (credits, gift cards and promo code)
shippingAddress | object | shipping address, can be used only for passing saved address identifier (if you have one), this will retrieve shipping address from saved address in your account (requires authorization).
billingAddress | object | address used for billing
paymentInfo | object | should be used only for passing payment account identifier (requires authorization), this will retrieve billing address from saved payment account in your account
taxAddress | object | address for tax calculations, should be used only when tax calculations are based on billing information and no billing needed for shopping cart (case when total value is fully covered with credits, purchase without payment)

<aside class="warning">
Load tax method does not send directly any credit card related information. Billing address should contain only address fields. You can only send payment account identifier in `paymentInfo` to get billing address from previously saved payment account.
</aside>

<aside class="notice">
You should never call this method via `http` protocol, use `https` for all Shopping Cart API calls.
</aside>

**Triggering tax amounts loading:**

When you load shopping cart details you got `taxSettings` object for every deal object. 
If taxes enabled for specific deal than you should trigger tax amount calculation at a certain time and `taxSettings` object will help you find out in which one.
In addition to all the other fields `taxSettings` contain `addressToUse` property which shows what changes will trigger tax amounts loading.

<aside class="notice">
It is recommended to do tax amount loading in async tasks on actions that affects total value (offers quantity change, shipping methods change, applying discounts etc) and on specific tax triggers described above.
</aside>

*`addressToUse` values:*

Value | Description
--------- | -----------
ORIGIN_ADDRESS | tax amount calculation is based on shipping address
CUSTOMERS_BILLING_ADDRESS | tax amount calculation is based on shipping address
SUB_MERCHANT_LOCATION | tax amount calculation is based on submerchant location, this type of taxes will be triggered when user selects merchant location attribute (will have `isLocation` flag from API version 7), if no location attributes available for deal than you should load tax once for deal after shopping cart loaded. 
PREDEFINED_STATE | tax amount calculation is based on predefined state, this value is predefined on backend, you should load tax once for deal after shopping cart loaded.

**Handling results:**

As a result of loading you'll get array with tax amounts objects which will help you calculate totals of your shopping cart and complete preparing for checkout.
You should include received amounts to shopping cart calculations as additional amount of money to pay.

*Tax amount object values:*

Parameter | Type | Description
--------- | --------- | -----------
gbId | number | deal identifier
offerId | number | offer identifier
amount | number | value of taxes calculated for the deal
includedToPrice | boolean | flag indicates that tax value is already included to offer price and should not be added to shopping cart total.
canPayTaxesWithCredits | boolean | currently ignored, always true
canPayTaxesWithGiftCards | boolean | currently ignored, always true
canPayTaxesWithPromoCode | boolean | currently ignored, always true

## Checkout

Shopping cart checkout is fully handled by ShoppingCart singletons. 
You should fill `OrderInfo` bean with your shopping cart details and call checkout method of `ShoppingCart`.
Shopping cart checkout is highly critical process, you should do all the possible validation checks before completing purchase.

> Performs shopping cart checkout

```shell
curl \
	-X POST \
	-d '{"dealsOrder":[{"dealId":2296952,"cityId":25221,"offers":[{"offerId":2970592,"count":1,"shippingMethodId":29352}]}],"shippingAddress":{"address":"Main street 1","address2":"","firstName":"First","city":"Beverly Hills","phone":"555-555555","zip":"90210","countryCode":"US","state":"California","lastName":"Last"},"billingAddress":{"address":"Main street 1","address2":"","firstName":"First","city":"Beverly Hills","phone":"555-555555","zip":"90210","countryCode":"US","state":"California","lastName":"Last"},"guestEmail":"test@user.com","paymentInfo":{"cardType":"visa","secureCode":"123","firstName":"First","cardNumber":"4111111111111111","expirationMonth":2,"expirationYear":2017,"lastName":"Last"}}' \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Authorization: {AUTHORIZATION_TOKEN}" \
	-H "Accept: application/json; charset=UTF-8" \
	-H "Content-Type: application/json; charset=UTF-8" \
	https://stage.nimblebuy.com/api/shoppingCart/makeBuy?merchantId=78
```

```objective_c
	NCShoppingCartOrderDiscounts *discounts = [[NCShoppingCartOrderDiscounts alloc] init];
	discounts.promoCode = {PROMO_CODE};
	discounts.giftCards = {GIFT_CARDS};
	discounts.credits = {USED_CREDITS};
	
	NCShoppingCartOrderInfo *orderInfo = [[NCShoppingCartOrderInfo alloc] init];
	orderInfo.discounts = discounts;
	orderInfo.guestEmail = {GUEST_EMAIL};
	orderInfo.birthDate = {BIRTH_DATE};
	orderInfo.merchantOptIns = {MERCHANT_OPT_INS};
	orderInfo.shippingAddress = {SHIPPING_ADDRESS};
	
	if (isCardless) {
		if (paymentAccount) {
			orderInfo.paymentInfo = [NCShoppingCartPaymentInfo paymentInfoFromPaymentAccount:paymentAccount];
		} else if (billingInfo) {
			orderInfo.paymentInfo = [NCShoppingCartPaymentInfo paymentInfoFromBillingInfo:billingInfo];
		}
		orderInfo.paymentInfo.cardCVV = secureCode;
	} else {
		if (hasBillingTaxes) {
			orderInfo.taxAddress = taxAddress;
		}
	}
	
	[[NCShoppingCart cart] checkoutWithOrderInfo:orderInfo handler:^(NSError *error) {
			if (error) {
				// Handle error
			} else {
				// Handle success
			}
		}];
```

```java
	ShoppingCart.OrderInfo orderInfo = new ShoppingCart.OrderInfo();
	
	orderInfo.discounts().promoCode({PROMO_CODE});
	orderInfo.discounts().giftCards({GIFT_CARDS});
	orderInfo.discounts().credits({USED_CREDITS});
	
	orderInfo.guestEmail({GEUEST_EMAIL});
	orderInfo.dateOfBirth({BIRTH_DATE});
	orderInfo.merchantOptIns({MERCHANT_OPT_INS});

	if (isCardless) {
		orderInfo.taxAddress(taxAddress);
	} else {
		if (null != paymentInfo) {
			orderInfo.paymentInfo(ShoppingCart.OrderPaymentInfo.newInstance(paymentInfo));
			if (null == paymentInfo.paymentAccountId()) {
				orderInfo.billingAddress(paymentInfo.billingAddress());
			}
		} else if (null != billingInfo) {
			if (null == billingInfo.id) {
				orderInfo.billingAddress(billingInfo.billingAddress());
			}
			orderInfo.paymentInfo(OrderPaymentInfo.newInstance(billingInfo));
			if (null != secureCode) {
				orderInfo.paymentInfo().cardCVV(secureCode);
			}
		}
	}
	orderInfo.shippingAddress(shippingAddress);
	ShoppingCart.getInstance().loadTaxAmounts(orderInfo, new ShoppingCart.ResultHandler() {
		@Override
		public void onSuccess() {
			// Handle success
		}

		@Override
		public void onFailure(NCResponse response) {
			// Handle error
		}, getActivity());
```

> Result JSON

```json
{
	"id": 71856942
}
```

*Request body JSON parameters:*

Parameter | Type | Description
--------- | --------- | -----------
dealsOrder | array | array of deals in shopping cart
discounts | object | map of used discounts for shopping cart (credits, gift cards and promo code)
shippingAddress | object | shipping address, can be used only for passing saved address identifier (if you have one), this will retrieve shipping address from saved address in your account (requires authorization).
billingAddress | object | address used for billing
paymentInfo | object | payment account id or full credit card information, credit card CVV is required for checkout.
taxAddress | object | address for tax calculations, should be used only when tax calculations are based on billing information and no billing needed for shopping cart (case when total value is fully covered with discounts, purchase without payment)
guestEmail | string | email for guest checkout, can't use saved payment accounts or addresses, full credit card information is required if purchase is not cardless (fully covered with discounts).
dateOfBirth | string | `en_US_POSIX` string with user birth date, optional field, should be filled if required.
merchantOptIns | array | list of merchant opt ins

<aside class="notice">
You should never call this method via `http` protocol, use `https` for all Shopping Cart API calls.
</aside>

<aside class="warning">
You should not store any payment related information on client side.
</aside>
