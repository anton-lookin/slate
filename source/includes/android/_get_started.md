## Get Started

### Setting Up the the Android Demo Application

* Required developer tools Android Studio & SDK

* Import gradle project as “Import Project…”

* There is hierarchy of module’s dependencies, project depends on actionbarsherlock-pulltorefresh library by Chris Banes (https://github.com/chrisbanes/ActionBar-PullToRefresh). Library sources included as source files in library directory and compiled as separated project.

* Demo application uses gradle (http://www.gradle.org/) and Android gradle plugin (http://tools.android.com/tech-docs/new-build-system/user-guide ) to perform build and distribution process. Application configuration script is build.gradle located inside SdkApplication module. Script contains dependencies description needed by application and automatically resolves this dependencies by loading necessary files from specified repositories. 

* Gradle.properties contains properties for application configuration. Currently it contains set of Google Maps V2 API keys for different build configuration.

### Third Party Libraries

* ActionBar-PullToRefresh - This project aims to provide a reusable Pull to Refresh widget https://github.com/chrisbanes/ActionBar-PullToRefresh. Resolved by gradle dependency on /libraries/actionbarsherlock-pulltorefresh.

* ActionBarSherlock - is an extension of the support library designed to facilitate the use of the action bar design pattern across all versions of Android with a single API. http://actionbarsherlock.com/. Resolved by gradle dependency on android library (com.actionbarsherlock:actionbarsherlock:4.4.0@aar)

* Android-ViewPagerIndicator - paging indicator widgets that are compatible with the ViewPager from the Android Support Library to improve discoverability of content https://github.com/JakeWharton/Android-ViewPagerIndicator. Resolved by gradle dependency on android library (com.viewpagerindicator:library:2.4.1@aar)

* Google play services - required for maps feature integration. Resolved by gradle dependency (com.google.android.gms:play-services:4.1.+)

### Merchant properties

File with app configuration for merchant located in /res/raw/app.properties
The file contains the following properties:

Name | Description
---- | ----
connection.url | merchant site url, required
connection.merchant | merchant id, required
connection.locale | merchant locale sites
connection.port | site port, 80 by default
connection.sslport | secure site port, 443 by default
connection.apiVersion | supported api version
config.country | merchant country code (uses for zip validation)
config.cookiesDayExpires | days to expires user login data, 60 by default
config.connTimeout | timeout connection to server API in millis, 60s by default
config.isShoppingCartEnabled | use shopping cart instead of usual buy page
config.isFreeOrdersEnabled | support free orders in application
config.locateStartCity | try to geolocate & select closest city

<aside class="notice">
Configuration for application locales, represented in connection.locale property
This is an example of how the value should look: 
connection.locale=6:en,256:fr
Where integer values 6, 256 is locale site ids & string values en, fr suffix of values directory for listed locales, e.g. /res/values-en, /res/values-fr.
</aside>

### Project structure

Application uses gradle flavour to provide flexible way of constructing multiple application variants. Demo application contains main directory with all sources and resources and BlueDotDeals flavour and overrides AndroidManifest.xml to add google services support for demo application (google maps and cloud messages). API keys and package identifiers will be replaced to actual one by gradle script when gradle sync is done.

### Creating additional flavour

To develop your own new application, flavour needs to be applied. You can copy BlueDotDeals file structure and gradle settings or just rename the BlueDotDeals file to meet your requirements. Any source code you need to override (has different behavior or needs code customization and enhancement) should be removed from main directory and added to all flavours working directories, note that source directory path should be the same in all flavours (example, /java/src/nc.app/auth/Account.java should have the same path in bluedotdeals folder if you want override Account.java class). 

### Customization

* Application Theme implemented according to Android API Guides (http://developer.android.com/guide/topics/ui/themes.html). 
* SherlockActionBar customization easy to change with this resource http://jgilfelt.github.com/android-actionbarstylegenerator/
* You can also customize the theme manually. It is /res/values/styles.xml includes styles which you can change. Also, as you know, it is possible to change drawable resources, layouts, dimens, etc.



