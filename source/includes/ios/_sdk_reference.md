## SDK Reference

NimbleCommerce iOS SDK classes are divided to three groups:
* manager classes - singleton classes that provides global access to shared actions and data. SDK contains settings, merchant and authorization managers. Authorization manager is used to perform authorization functions.
* helper classes - container and bean classes that used to perform API requests, present and store received from responses data.
* request classes - wrapper classes that used to perform specific NimbleCommerce API calls.
* beans classes - container objects to keep your data incapsulated

### NimbleCommerce iOS SDK Request Classes Description

Every request class is used to perform specific NimbleCommerce API call (get deal, save payment account, etc.). Every request class is wrapper around loader class which performs network communication and parses server response. Request classes use block-based and delegation pattern to notify about successful or failed call status. In case of successful response, received data is stored in object fields and can be accessed using object properties. Any detailed description about available actions and structure of NimbleCommerce iOS SDK classes can be found in your merchant portal in Mobile Apps section. Documentation is distributed in .docset format that allows you to import it in Xcode Documentation system.

### Recommendations

Loader classes usage remains similar if you use ARC or doesn’t. Strong reference on loader object should be saved in field or property to keep loader live while loading routines are in progress. Loader can be created on initialization or can be instantiated lazily on first access to property. Examples for loaders initialization and usage for arc and manual memory management. Note that result blocks of loaders will be released and reset to nil as soon as loader completes, you should set result blocks before every loader execution.

Every loader extends NCLoader class that provides common interface for NimbleCommerce API calls. Every NCLoader has configuration fields described below, that will be set up automatically from managers or can be set up manually to configure loader with specific data.

NCLoader configuration fields:

Class | Description
----- | -----
userAgent | application user agent, used to identify application on backend.
apiVersion | NimbleCommerce API version.
merchantId | merchant identifier.
secretKey | application secret key.
localeId | locale identifier.
authorizationKey | authorization key, used only for API calls that require user authorization.

### Framework contains list of loaders for public API methods that can be devided on categories:

Account related request classes:

Class | Description
----- | -----
NCAuthorization | used to authorize user.
NCAccountSignUp | used to perform new user registration action.
NCAccountLocaleSet | used to install specific locale to user.
NCAccountSettingsGet | used to receive usr account specific data.
NCAccountSettingsSet | used to update user account data.
NCEmailVerify | used to initiate email verification process.
NCEventTrack | used to track events.
NCPasswordRecover | used to initiate password recovery process.
NCUserDealsList | used to receive list of deals for authorized user.
NCCouponRedeem | used to redeem specific coupon.
NCDailyAlertsSubscribe | used to subscribe for category alerts.
NCOrdersList | used to get orders list for authorized user.
NCOrderVouchersGet | used to get list of order vouchers for authorized user.
NCStoreOfferClaim | used to claim store offer.
NCOrderEmailSend | used to send email with order details.

Payment accounts related request classes:

Class | Description
----- | -----
NCPaymentAccountDelete | used to delete user payment account.
NCPaymentAccountGet | used to receive user payment account data.
NCPaymentAccountSave | used to save or create user payment account.
NCPaymentAccountSetDefault | used to set default user payment account.
NCPaymentAccountsList | used to receive user payment accounts list.

Addresses related request classes:

Class | Description
----- | -----
NCAddressesList | used to receive user addresses list.
NCAddressSave | used to save/create user address.
NCAddressValidate | used to validate address data.
NCAddressDelete | used to delete user address.
NCAddressSetDefault | used to set user address as default.

Application related request classes:

Class | Description
----- | -----
NCApplicationSettingsGet | used to receive application settings.
NCLocaleSettingsGet | used to receive locale specific settings.
NCMerchantCategoriesGet | used to receive categories list.
NCMerchantTagsGet | used to receive tags list.

Push notifications related request classes:

Class | Description
----- | -----
NCPushNotificationDeviceRegister | used to assign push notification device token to specific user.
NCPushNotificationSettingsGet | used to get push notification alerts settings.
NCPushNotificationSettingsSet | used to save push notification alerts settings.

Deal infromation related request classes:

Class | Description
----- | -----
NCClosestCityGet | used to get closest city by geolocation.
NCClosestDealGet | used to get closest deal by geolocation.
NCContestDealJoin | used to join contest deal.
NCDealGet | used to receive specific deal common data.
NCDealsList | used to receive list of deals.
NCUserShortDealGet | used to receive user deal data specific to user.
NCDealShareOptionsGet | used to get deal sharing options.

Buy request classes:

Class | Description
----- | -----
NCDealTaxGet | used to calculate deal tax using zip code.
NCPromoCodeValidate | used to validate and calculate promo code amount using promo code.
NCGiftCardValidate | used to validate and get gift card balance.
NCBuyDealGet | used to receive specific deal data sufficient to perform buy action (deprecated, shopping cart methods should be used instead).
NCDealBuy | used to buy deal(deprecated, shopping cart methods should be used instead).
