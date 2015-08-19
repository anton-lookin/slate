# Authorization

NimbleCommerce use custom authentication/authorization scheme which is similar to Basic HTTP Authentication. The process is simple enough, to authorize and authenticate user do a secure call over https on /api/authorization with credentials or facebook data. You get authorization token which should be passed with requests that requires user authorization/authentication. Authorization token should be placed in HTTP request with `Authorization` header name. Server application will extract and validate user identity from request automatically.


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