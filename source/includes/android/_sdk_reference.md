## SDK Reference

### Loading Deals

This is a simple overview of SDK main components and their usages. To get full understanding of application components and SDK usage please use demo application source code.

> To load deals by different criterias there is gb.android.sdk.Deals async request

```java
new Deals(context)
	.with(params)
	.resultHandler(new Deals.ResultHandler() {
		@Override
		public void onSuccess(GroupBuyList deals) {
			// Do something with deals
			// put to adapter, increase page, etc.
		}
		@Override
		public void onError(String message) {
			// Show error
			// Do some other stuff
		}
	})
	.execute(); 
```

> To pass necessary criteria to request there is gb.android.sdk.beans.Params. Filling Params build to setup deals loader

```java
	new Params.Builder()
		.set(tags) // load deals with specified tags only
// only specified types
		.set(Constants.Type.DEAL, Constants.Type.AFFILIATE)
// set up sorting
		.set(Constants.Sort.PUBLISHED_DESC, Constants.Sort.DISTANCE_ASC)			.category(cityId) // current city id
		.location(latitude, longitude) // obtained location
		.page(nextPage) // next page to load
		.size(listSize) // deals list size
		.build();
```

> To get cities there is simple call:

```java
List<Category> categories = Nmb.getDealsManager().getMerchantCategories();
```

> To get tags everywhere in code just use next listing

```java
List<MerchantTag> tags = Nmb.getMerchantManager().getMerchantTags();
```
 
> To authorize SDK to load resources which associated with specific customer should be used next listing, call authorize method in async context.

```java
Nmb.getAuthManager().authorize(email, password);
```

> To check if SDK has authorization

```java
Nmb.getAuthManager().isAuthorized();
```

> After the successful authorization SDK get Authorization Token with 24 hours expiration. Also keeps in secure storage customer credentials. Call for reauthorization when authorization token expired error code returned

```java
Nmb.getAuthManager().reauthorize();
```

> To completely clear authorization and saved credentials from SDK should be called

```java
Nmb.getAuthManager().deauthorize();
```

> To create new user in the system there is gb.android.sdk.request.SingUpUser async request, pass a valid UserBean to this request.

```java
new SignUpUser(context)
	.user(user)
	.resultHandler(new SignUpUser.ResultHandler() {
		@Override
		public void onSuccess(UserBean user) {
			// Do authorize with user.email and user.password
		}
		@Override
		public void onError(String message) {
			// Show error
			// Do some other stuff
		}
	})
	.execute();
```

> To authorize SDK using facebook, should be used listing below. If system has restriction on customer age limit and facebook bean has not the field, dateOfBirth parameter should be passed to method.

```java
Nmb.getAuthManager().authorize(facebook, dateOfBirth);
```

> To get user data there is gb.android.sdk.request.GetUserSettings.

```java
new GetUserSettings(context)
	.resultHandler(new GetUserSettings.ResultHandler() {
		@Override
		public void onSuccess(UserBean user) {
			// Use user data
		}
		@Override
		public void onError(String errorMessage) {
			// Show error
			// Do some other stuff
		}
	})
	.execute();
```

> To get user purchases should be called asynchronously method below:

```java
GroupBuyList offersList = Nmb.getDealsManager().getMyCouponsList(page, filter);
```

> Load free orders (Store Offer)

```java
new Orders(context)
	.filter(filter) // may be available or used
	.offset(offset) // usually page * limit
	.limit(limit) // size of elements to load
	.resultHandler(new Orders.ResultHandler() {
		@Override
		public void onSuccess(List<Order> orders, Iteger totalCount) {
			// Process orders
			// Set to adapter
		}
		@Override
		public void onError(String message) {
			// Show error
			// Do some other stuff
		}
	})
	.execute();
```

> Vouchers (for free offers only). Load vouchers for particular order

```java
new Vouchers(context)
		.orderId(orderId)
		.resultHandler(new Vouchers.ResultHandler() {
			@Override
			public void onSuccess(List<Voucher> vouchers) {
				// render vouchers
			}
			@Override
			public void onError(String message) {
				// show error
				// Do other stuff
			}
		})
		.execute();
```

### Push Notifications
To subscribe device to push notifications should add registration and for logged in user only. Use listing below only if SDK has authorization for particular customer. For sure reqistrationId should be obtained in usual way in according to Android Documentation. Also should be implemented receiver and intent service to handle and show notifications, see Documentation and nc.app.GcmIntentService, nc.app.GcmBroadcastReceiver from demo application to get details.

> Register device token on server

```java
new RegisterDevice(context)
	.registrationId(registrationId)
	.execute();
```

> To unregister device from push notification use listing below

```java
new UnregisterDevice(context)
	.registrationId(registrationId)
	.execute();
```

### Shopping Cart

> Add deal to Shopping Cart, just use

```java
ShoppingCart.getInstance()
	.addItem(
	new ShoppingCartItem().categoryId(categoryId).dealId(dealId),
		new ShoppingCart.ResultHandler() {
			@Override
			public void onSuccess() {
				// open Shopping Cart activity
				// or do some other stuff
			}
			@Override
			public void onFailure(NCResponse response) {
				// show error
				// if response.getErrorCode() exists
				// there is no compatible deals
			}
		}, context);
```

> To clear Shopping Cart use listing below

```java
	ShoppingCart.getInstance().clear();
```

> Perform loading of Shopping Cart details

```java
	ShoppingCart.getInstance()
		.load(new ShoppingCart.ResultHandler() {
			@Override
			public void onSuccess() {
				// render and do other stuff
			}
			@Override
			public void onFailure(NCResponse response) {
				// perform failure stuff
			}
		}, context);
```

> Use required information about Shopping Cart and set to view.

```java
	ShoppingCart shoppingCart = ShoppingCart.getInstance();
	List<ShoppingCartItem> items = shoppingCart.items();
	List<ShortDeal> deals = shoppingCart.deals();
	BillingSettings billing = shoppingCart.billingSettings();
	BuyOptions options = shoppingCart.buyOptions();
	UserBillingSettings settings = shoppingCart.userBillingSettings();
```


### Deals, Offers, Attributes, etc.

Particular item of gb.android.sdk.cart.ShoppingCartItem contains data related to selected info performed by user like gift information, shipping method, quantity, attributes.
Original data for deals contains in gb.android.sdk.beans.ShortDeal. There are additional  data points related to deal, offers, attributes, shipping, etc.
To render deals to view, you will need to use the following 2 items: (1) original data about deals from ShortDeal and (2) data for selections from ShoppingCartItem.

### Prices and limits

Each offer has field ‘price’ which should be applied for calculations.
Be careful with limits, particular customer able to select only allowed limit of deal.

`shortOffer.quantity` - available quantity to select for the offer
`shortOffer.giftQuantity` - available quantity to gift the offer

`shortDeal.quantityLimit` - available quantity for all child offers
`shortDeal.quantityGiftLimit` - available quantity to gift for all child offers

Deal can be gifted only if `shortDeal.options.hasGifting` is `TRUE`.

### Tax

> If particular deal has option shortDeal.options.hasTax=true additional price for shipping should be calculated. Use listing below to do that:

```java
	new CalculateTaxPercent(context)
		.address(selectedAddress) // gb.android.sdk.beans.Address
		.dealIds(dealIdList) // all deals that has options.hasTax=true
		.resultHandler(new CalculateTaxPercent.ResultHandler() {
			@Override
			public void onSuccess(Map<String, Double> taxRates) {
				// taxRates has key as dealId & value as taxAmount
				// should be applied to price of deal
			}
			@Override
			public void onError(String errorMessage) {
				// show error
				// do some other stuff
			}
		})
		.execute();
```

### Shipping Price (Deal & Offer level)

`gb.android.sdk.beans.ShortDeal` has optional field `shippingType`. If field exists additional price for shipping should be applied for this deal.
To get price: if `shortDeal.shippingType.methods` exist use price from particular item which selected by user, if not exist - use `shippingType.price`

To know what kind of shipping need to apply to deal use `shippingType.pricingType`
`PricingType.PER_DEAL` - should be applied once for deal
`PricingType.PER_MERCHANT` - the same, but if more deals with the same shortDeal.subMerchantId exists should be applied greatest value once.

`gb.android.sdk.beans.ShortOffer` also has field `shippingType`. If field exist additional price for shipping should be applied for this offer.
To get price: if `shortOffer.shippingType.methods` exist use price from particular item which selected by user, if not exist - use `shippingType.price`

To know what kind of shipping need to apply to offer use `shippingType.pricingType`
`PricingType.PER_OFFER` - should be applied once for this offer, because each offer has own shipping price
`PricingType.PER_ITEM` - every offer item should be multiplied on shipping price, so price is `offerPrice * quantity + shippingPrice * quantity`

> An promotion code available to be applied to order once. To get discount value which will be applied use listing below.

```java
	new PromoCodeAmount(context)
		.dealIds(dealIdsList) // only shortDeal.options.hasPromoCode=true
		.promoCode(promoCodeString) // promotion code
		.resultHandler(new PromoCodeAmount.ResultHandler() {
			@Override
			public void onSuccess(PromoCodeAmount.Response promo) {
				// apply price of promo code
				// promo.type may be AMOUNT or PERCENT
				// promo.value is amount to apply
			}
			@Override
			public void onError(PromoCodeAmount.Response response) {
				// show error, do some other stuff
			}
		})
		.execute();
```

> One or more gift cards available to apply to order. Use listing below to get information about particular gift card.

```java
	new CheckGiftCard(context)
		.cardNumber(numberString) // gift card number
		.cardPin(pinString) // gift card pin
		.resultHandler(new CheckGiftCard.ResultHandler() {
			@Override
			public void onSuccess(GiftCard giftCard) {
				// apply gift card amount giftCard.amount
			}
			@Override
			public void onError(String errorMessage) {
				// show error, do other stuff
			}
		})
		.execute();
```

### Credits

If logged in user has credits available this discount may also be applied. 
To get the amount of available credits use `order.userBillingSettings().credits()`
Note: credits may be applied only for deals with `shortDeal.options.hasCredits=true`

### Addresses

An address should be applied to order if at least one product was added.
If save address feature is enabled for merchant predefined address available to use.

> To get the list of addresses for a logged-in user use the code below:

```java
	new LoadAddresses(context)
		.resultHandler(new LoadAddresses.ResultHandler() {
			@Override
			public void onSuccess(LoadAddresses.Response response) {
				// render addresses to list view
				// List<Address> addresses = response.addresses
			}
			@Override
			public void onError(String errorMessage) {
				// show error, do other stuff
			}
		})
		.execute();
```

> It is possible to also change any address or create new one using listing below.

```java
	new SaveAddress(context)
		.address(bean) // gb.android.sdk.beans.Address
		.resultHandler(new SaveAddress.ResultHandler() {
			@Override
			public void onSuccess(Address address) {
				// do some stuff with address
			}
			@Override
			public void onError(String errorMessage) {
				// show error, do other stuff
			}
		})
		.execute();
```

> Delete option also available

```java
	new DeleteAddress(context)
		.addressId(id)
		.resultHandler(new DeleteAddress.ResultHandler() {
			@Override
			public void onSuccess(NCResponse response) {
				// refresh view, etc.
			}
			@Override
			public void onError(String errorMessage) {
				// show error, do other stuff
			}
		})
		.execute();
```

### Payment Accounts

An credit card and billing information should be applied to order if total amount of order greater than 0. The user should also be able to use their saved credit card, if one is available to use. 

> To load saved payment information use listing:

```java
	new LoadPaymentInfos(context)
		.resultHandler(new LoadPaymentInfos.ResultHandler() {
			@Override
			public void onSuccess(LoadPaymentInfos.Response response) {
				// render response.paymentInfos
			}
			@Override
			public void onError(String errorMessage) {
				// show error, other stuff
			}
		})
		.execute();
```

> It is also possible to change any payment method information or create a new one using the code below:

```java
	new SavePaymentInfo(context)
		.paymentInfo(bean)
		.resultHandler(new SavePaymentInfo.ResultHandler() {
			@Override
			public void onSuccess(SavePaymentInfo.Response response) {
				// use response.paymentAccountId				
			}
			@Override
			public void onError(String errorMessage) {
				// show error, do other stuff
			}
		})
		.execute();
```

> Delete option also available

```java
	new DeletePaymentAccount(context)
		.paymentAccountId(id)
		.resultHandler(new DeletePaymentAccount.ResultHander() {
			@Override
			public void onSuccess(NCResponse response) {
				// update list view
	}
	@Override
	public void onError(String message) {
		// show error, do other stuff
	}
	})
	.execute();
```

### Order

Before checkout is completed ShoppingCart.CheckoutInfo this should be created.
This object has fields which represent additional data that should be passed to server API to make complete the proper checkout of the shopping cart. 

`guestEmail` - if SDK does not have authorization and guest checkout enabled for all deals, the field should be passed;
`dateOfBirth` - in format MM/dd/yyyy, should be used next listing to know if show: `Nmb.getMerchantManager().isShownField(SIGN_UP, DATE_OF_BIRTH);`
`credits` - amount of credits which has been applied by the user
`promoCode` - string value of promotion code which has been applied by the user
`shippingAddress` - `gb.android.sdk.beans.Address`, required if Shopping Cart has at least one product or deal card.
`billingInfo` - `gb.android.sdk.beans.PaymentInfo`, required if the total amount of shopping cart value is greater than 0, if field exist credit card will be charged.
`secureCode` - credit card cvv code
`giftCards` - list of gb.android.sdk.beans.GiftCardBuyBean, which applied by user

> Finally, to complete checkout just use the listing below :

```java
	ShoppingCart.getInstance().checkout(checkoutInfo, 
			new ShoppingCart.ResultHandler() {
				@Override
				public void onSuccess() {
					// show confirmation view
					ShoppingCart.getInstance().clear();
				}
				@Override
				public void onFailure(NCResponse response) {
					// show error, do other stuff
				}
			}, context);
```