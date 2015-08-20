# Purchases

You can load your purchases list by calling `/api/userDealList.action`. 
This call allows you to receive user purchase list with filters for authorized user.

<aside class="notice">
This method has basic pagination functionality, but it does not allow to select page size and it is hardcoded on a backend to return pages with maximum 5 items in list.
</aside>

Parameters for purchases loading API call:

Key | Type | Description
--------- | --------- | -----------
page | number | list page to load
filter | string | filtering parameter

Filter parameter constants:

Value| Description
--------- | -----------
available | available purchases
expired | expired purchases
used | used purchases
received | received gifts
sent | sent gifts
membershipAvailable | available rewards
membershipUsed | used rewards

> Load purchases list

```objective_c
	self.loader = [[NCUserDealsList alloc] init];
	self.loader.delegate = self;
	
	[self.loader getDealsWithFilter:NCUserDealListFilterAvailable page:0];
	
	-(void) userDealsListingSucceed:(NCUserDealsList *)loader {
		// Handle loader.deals
	}
	-(void) userDealsListingFailed:(NCUserDealsList *)dataSource withError:(NSError *)error {
		// Handle request error
	}
```

```java
	// AsyncTask class example for purchases list loading
	private class DealsLoader extends AsyncTask<Void, Void, Void> {
		@Override
		protected void onPreExecute() {
		}

		@Override
		protected Void doInBackground(Void... params) {
			GroupBuyList offersList = null;
			try {
				offersList = Nmb.getDealsManager().getMyCouponsList(mCurrentPage, filter);
			} catch (ServerIsDownException | ConnectionException | ConnectionTimeoutException e) {
				e.printStackTrace();
				// Handle request exception
			}

			if (null != offersList && offersList.totalCount != null) {
				// Save received purchase list for further processing
			}
			return null;
		}

		@Override
		protected void onPostExecute(Void aVoid) {
			if (null == getView()) {
				return;
			}
			// Handle results
		}
	}
```

```shell
	curl \
	-X GET \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Accept: application/json; charset=UTF-8" \
	-H "Content-Type: application/json; charset=UTF-8" \
	-H "Authorization: MTQ0MDA1NDcwNjU4Nzo6OjEwMjk4NTgzMjo6OnJCTzhWN2hxb0p5ZEFjQldUMDR2VWc9PTo6OlNVQlNDUklCRVJfSUQ=" \
	"http://stage.nimblebuy.com/api/userDealList.action?merchantId=78&page=0&filter=available"
```