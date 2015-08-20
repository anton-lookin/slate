# Order Status

From NimbleCommerce API version 6 you have access to order status loading methods. 
This methods include loading order details, view and redeem order vouchers.
You can check order status without registering user account, you need only email and order number to proceed.
First thing you need to do is perform partial authorization and get temporary authorization key, which is required to access order status API methods.

> Authorize for orders resource

```objective_c
	self.authorizator = [[NCAuthorization alloc] init]
	self.authorizator.onSuccess = ^(NCLoader *loader) {
		// auth token returned in loader.authorizationKey
	};
	self.authorizator.onFailure = ^(NCLoader *loader, NSError *error) {
		// handle error
	};
	// Authorizes for orders resource for given email.
	[self.authorizator authorizeForResource:@"orders" withEmail:@"user@email.com"];
```

```java
	new AuthorizeResource(this).resource("orders").email("user@email.com").resultHandler(new AuthorizeResource.ResultHandler() {
		@Override
		public void onSuccess(final String authKey, final Boolean hasAccount) {
			// handle success
		}

		@Override
		public void onError(String message) {
			// handle error
		}
	}).execute();
```

```shell
curl \
	-X POST \
	-d "email=user@email.com&resource=orders" \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Accept: application/json; charset=UTF-8" \
	https://stage.nimblebuy.com/api/authorize?merchantId=78
```

> Load order details

```objective_c
	self.loader = [[NCOrderGet alloc] init];
	self.loader.authorizationKey = authorizationKey;
	self.loader.onSuccess = ^(NCLoader *loader) {
		// handle loader.order
	};
	self.orderLoader.onFailure = ^(NCLoader *loader, NSError *error) {
		// handle request handler
	};
	[self.loader getPaidOrder:orderNumber];	
```

```java
	new LoadOrder(getActivity()).orderId(orderNumber).resultHandler(new LoadOrder.ResultHandler() {
		@Override
		public void onSuccess(Order order) {
			// handle request success
		}
		@Override
		public void onError(String message) {
			// handle request error
		}
	}).authorizationKey(authorizationKey).execute();
```

```shell
curl \
	-X GET \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Accept: application/json; charset=UTF-8" \
	-H "Authorization: MTQ0MDA1NDcwNjU4Nzo6OjEwMjk4NTgzMjo6OnJCTzhWN2hxb0p5ZEFjQldUMDR2VWc9PTo6OlNVQlNDUklCRVJfSUQ=" \
	https://stage.nimblebuy.com/api/orders/paid/1234?merchantId=78
```

> Load order items (for product deals only)

```objective_c
	self.itemsLoader = [[NCOrderItemsGet alloc] init];
	self.itemsLoader.authorizationKey = authorizationKey;
	self.itemsLoader.onSuccess = ^(NCLoader *loader) {
		// Handle loader.items array
	};
	self.itemsLoader.onFailure = ^(NCLoader *loader, NSError *error) {
		// Handle request error
	};
	[self.itemsLoader getPaidOrderItems:orderNumber];
```

```java
	new LoadOrderItems.Paid(getActivity()).orderId(orderNumber).resultHandler(new LoadOrderItems.ResultHandler() {
		@Override
		public void onSuccess(List<OrderItem> orderItems) {
			// Handle request success
		}
		@Override
		public void onError(String message) {
			// Handle request error
		}
	}).authorizationKey(authorizationKey).execute();
```

```shell
curl \
	-X GET \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Accept: application/json; charset=UTF-8" \
	-H "Authorization: MTQ0MDA1NDcwNjU4Nzo6OjEwMjk4NTgzMjo6OnJCTzhWN2hxb0p5ZEFjQldUMDR2VWc9PTo6OlNVQlNDUklCRVJfSUQ=" \
	https://stage.nimblebuy.com/api/orders/paid/1234/items?merchantId=78
```

> Load order vouchers (for non product deals only)

```objective_c
	self.vouchersLoader = [[NCOrderVouchersGet alloc] init];
	self.vouchersLoader.authorizationKey = authorizationKey;
	self.vouchersLoader.onSuccess = ^(NCLoader *loader) {
		// Handle loader.vouchers array
	};
	self.vouchersLoader.onFailure = ^(NCLoader *loader, NSError *error) {
		// Handle request error
	};
	[self.vouchersLoader getPaidOrderVouchers:orderNumber];
```

```java
	new LoadOrderVouchers.Paid(getActivity()).orderId(orderNumber).resultHandler(new LoadOrderVouchers.ResultHandler() {
		@Override
		public void onSuccess(List<OrderVoucher> orderVouchers) {
			// Handle request success
		}
		@Override
		public void onError(String message) {
			// Handler request error
		}
	}).authorizationKey(authorizationKey).execute();
```

```shell
curl \
	-X GET \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Accept: application/json; charset=UTF-8" \
	-H "Authorization: MTQ0MDA1NDcwNjU4Nzo6OjEwMjk4NTgzMjo6OnJCTzhWN2hxb0p5ZEFjQldUMDR2VWc9PTo6OlNVQlNDUklCRVJfSUQ=" \
	https://stage.nimblebuy.com/api/orders/paid/1234/vouchers?merchantId=78
```