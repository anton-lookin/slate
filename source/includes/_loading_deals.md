# Loading Deals

You can load various deals list by calling `/api/groupBuyMerchantList` with set of parameters. 
This call does not require to have authenticated user to proceed. 

###Parameters description:
Parameters should be passed as GET request parametes in URL. `merchantId`, `localeId` and `categoryId` are mandatory parameters and all other are optional.

Key | Type | Description
--------- | --------- | -----------
merchantId | number | merchant identifier
localeId | number | locale site identifier
categoryId | number | category (city) identifier
page | number | list page to load, will check for maxResults to calculate valid list offset
maxResults | number | size of list page
type | string | comma separated string with deal types
filter | string | comma separated string with deal statuses or custom filter string
sort | string | comma separated string with deal sort constants
latitude | number | location latitude
longitude | number | location longitude
tags | string | comma separated tags (categories) identifiers

Deal types constants:
Constant | Description
--------- | -----------
d | paid deals
c | contests
s | store offers
m | membership deals
a | affiliate deals
t | travel deals
all | all deals

Deal status constants:
Constant | Description
--------- | -----------
l | live deals
c | closed deals
t | tipped deals
p | pending deals
e | expired deals
s | sold out deals
lt | live and tipped deals
lnt | live and not tipped deals
ct | closed and tippded deals
cnt | closed and not tipped deals

Deal sorts constants:
Suffix _asc or _desc should be added to sort parameters to proceed.
Constant | Description
--------- | -----------
sortField | sort by sort filed
distance | sort by distance
dealName | sort by deal name
published | sort by publish date
time | sort by expiration time
numberDealSold | sort by sold products count
stdCityName | sort by standart city name

> Loading deals list

```objective_c
	// Create loader and keep strong reference on it
	self.loader = [[NCDealsList alloc] init];
	// Assign loader delegate to handle loading result
	self.loader.delegate = self;
	// Aggregate pararameters in special bean
	NCDealListParameters *params = [[NCDealListParameters alloc] init];
	params.categoryId = @25221;
	params.page = 0;
	params.limit = 10;
	params.types = @"d,c,a"; // paid deals, contests and affiliates
	params.filters = @"l,e"; // load live and expired deals
	[params.sorts addSort:@"published_desc"] // add sorting parameter 
	params.tags = @[]; // array of tags to filter with
	[self.loader getDeals:params];
	
	// Handle loading success in delegated method
	-(void) dealsListingSucceed:(NCDealsList *)dataSource {
	}
	// Handle loading failure in delegated method
	-(void) dealsListingFailed:(NCDealsList *)dataSource withError:(NSError *)error {
	}
```

```java
	// Create parameter builder for deals loader
	Params.Builder builder = new Params.Builder().fromArgs(getArguments());
	builder.page(mCurrentPage).size(10).add(Constants.Sort.SORT_FIELD_DESC).add(Constants.Status.LIVE).add(Constants.Type.deals());

	final Deals loader = new Deals(getActivity());
	// Save reference on a loader
	this.loader = loader;
	loader.with(builder.build())
			.resultHandler(new Deals.ResultHandler() {
				@Override
				public void onSuccess(GroupBuyList dealsList) {
					// Handle loading success
				}

				@Override
				public void onError(String errorMessage) {
					// Handle loading failure
				}
			}).execute();
```

```shell
curl \
	-X GET \
	-H "x-nimblecommerce-api-version: 7" \
	-H "Accept: application/json; charset=UTF-8" \
	-H "Content-Type: application/json; charset=UTF-8" \
	"http://stage.nimblebuy.com/api/groupBuyMerchantList?merchantId=78&localeId=281&categoryId=25221&page=0&maxResults=10&type=d%2Cc%2Cs%2Ca&sort=sortField_desc"
```