## Get Started

1. The NimbleCommerce SDK for iOS is designed to be easy to install and use. This guide walks you through the basics of setting up the development and project environment and creating a simple application.

2. Install prerequisites. OS X and Xcode is required for iOS development. Xcode is available through the App Store.
Download NimbleCommerce iOS SDK. NimbleCommerce iOS SDK is distributed as framework and available to download in MobileApps section at your merchant portal. Keep an eye on updates, replace framework included to your projects, when new version become available.

3. Create and configure an Xcode project. Create a new Xcode project, add NimbleCommerce iOS SDK by dragging nc-ios-sdk.framework into the Frameworks section of your Project navigator. Framework is distributed in debug and release versions, debug version doesn’t strip debug messages logging code, to let developer see debug information.
The SDK relies on four other frameworks/libraries (CFNetwork, MobileCoreServices, SystemConfiguration and libz). To add these, go to the `Link Binary with Libraries` of the target’s Build phases pane, and click the `plus` button to add them.

4. Configure NimbleCommerce application. Basic configuration is made using NCSettings.plist file, which should be created in main bundle of your application. It is recommended to use NCSettings.plist from NimbleCommerce Demo Application as example.

NCSettings.plist required fields:

Key | Value
--------- | ---------
version | settings file version, number value displaying version of configuration file. You should increment this value every time you made changes to apply them on next application run.
merchantId | NimbleCommerce merchant identifier. Unique merchant identifier used in by NimbleCommerce API calls.
identifier | NimbleCommerce mobile application identifier. 
secretKey | NimbleCommerce mobile application secret key. String value used by all NimbleCommerce API calls to validate application permission to use NimbleCommerce API.
apiUrl | NimbleCommerce API root url. Root url used to perform API calls.
apiSecureUrl | NimbleCommerce API root secure url. Root url used to perform secure API calls (Usually the same as url as apiUrl but with https protocol).
countryCode | application country code (ISO 3166-1 alpha-2 code). Used to perform country specific fields validation (zip code, state, etc).
cardTypes | array of supported by application credit card types. Array should contain at least one card type. 

`NCSettingsManager` is singleton that manages application settings. It is main application manager and it should be configured before performing any API calls. To perform configuration you should send configure message to sharedInstance of NCSettingsManager.

Settings manager will dispatch couple of notifications about its configuration process status changes. Basically, you should add observers for NCSettingsManagerConfigureFinishNotification and NCSettingsManagerConfigureFailNotification, where you should handle successful or failed configuration.

`NCMerchantManager` is singleton that manages merchant specific settings and data, currently it is list of categories and tags. To perform configuration you should complete settings manager configuration and send configure message to sharedInstance of NCMerchantManager. 

Merchant manager will dispatch couple of notifications about its configuration process status changes. You should add observers for NCMerchantManagerConfigureFinishNotification and NCMerchantManagerConfigureFailNotification, where you should handle successful or failed configuration. Merchant manager usage is optional, NimbleCommerce SDK classes does not use merchant manager directly, it is used by demo application to provide common access to merchant specific data. You can implement your own scheme for handling merchant tags and categories and related data.

