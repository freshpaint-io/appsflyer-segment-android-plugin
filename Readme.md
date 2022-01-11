<img src="https://www.appsflyer.com/wp-content/uploads/2016/11/logo-1.svg"  width="200">


# AppsFlyer - Freshpaint Integration

----------
In order for us to provide optimal support, we would kindly ask you to submit any issues to support@appsflyer.com

*When submitting an issue please specify your AppsFlyer sign-up (account) email , your app ID , production steps, logs, code snippets and any additional relevant information.*


# Overview

AppsFlyer SDK provides app installation and event tracking functionality. We have developed an SDK that is highly robust (7+ billion SDK installations to date), secure, lightweight and very simple to embed.



You can track installs, updates and sessions and also track additional in-app events beyond app installs (including in-app purchases, game levels, etc.) to evaluate ROI and user engagement levels.

---

Built with AppsFlyer Android SDK `v6.4.3`

## Table of content

- [Quick Start](#quickStart)
-  [SDK Initialization](#sdk_init)
-  [Tracking In-App Events](#adding_events)
-  [Get Conversion Data](#conversion_data)
- [Unified Deep Linking](#deep_linking)


### <a id="whatIsFreshpaint">
# Introduction

Freshpaint makes it easy to send your data to AppsFlyer. Once you have tracked your data through Freshpaint's open source libraries, the data is translated and routed to AppsFlyer in the appropriate format. AppsFlyer helps marketers to pinpoint targeting, optimize ad spend and boost ROI.


### <a id="quickStart">

# Setting up the SDK

#### Adding the Plugin to your Project

Add the AppsFlyer Freshpaint Integration dependency to your app `build.gradle` file.
```java
compile 'io.freshpaint.android.integrations:appsflyer:0.1.0'
compile 'com.android.installreferrer:installreferrer:2.1'
```

#### Setting the Required Permissions

The AndroidManifest.xml should include the following permissions:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
```

### <a id="sdk_init"> Init AppsFlyer

```java

static final String FRESHPAINT_WRITE_KEY = "<YOUR_KEY>";

@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

Freshpaint.Builder builder = new Freshpaint.Builder(this , FRESHPAINT_WRITE_KEY)
.use(AppsflyerIntegration.FACTORY)
.defaultProjectSettings(
    new ValueMap()
        .putValue(
            "integrations",
            new ValueMap()
                .putValue(
                    "AppsFlyer",
                    new ValueMap()
                        .putValue("appsFlyerDevKey", "<your dev key>")
                        .putValue("trackAttributionData", true))))

...
(optional)

.logLevel(Freshpaint.LogLevel.VERBOSE)
.recordScreenViews()
.trackAttributionInformation() // Install Attributed event
.trackApplicationLifecycleEvents() // Application Opened , Application Updated , Application Installed events
.build();

Freshpaint.setSingletonInstance(builder.build());

}
```

Adding `.trackAttributionInformation()` will send the `Install Attributed` event to AppsFlyer.
Adding `.trackApplicationLifecycleEvents()` will send   `Application Opened`  , `Application Updated`  and `Application Installed` events to AppsFlyer.




## <a id="adding_events"> Track

When you call `track`, Freshpaint translates it automatically and sends the event to AppsFlyer.

Freshpaint includes all the event properties as callback parameters on the AppsFlyer event, and automatically translates `properties.revenue` to the appropriate AppsFlyer purchase event properties based on Freshpaint's spec’d properties.

Finally, Freshpaint automatically uses AppsFlyer’s transactionId-based de-duplication when sending an an `orderId`.


Purchase Event Example:
```java
Map<String, Object> eventValue = new HashMap<String, Object>();
eventValue.put("productId","com.test.id");
eventValue.put("revenue","1.00");
eventValue.put("currency","USD");

Freshpaint freshpaint = Freshpaint.with(this);
Properties properties = new Properties();
properties.putAll(eventValue);

freshpaint.track("purchase", properties);
```

Note: AppsFlyer will map `revenue -> af_revenue` and `currency -> af_currency`.

Check out the Freshpaint docs on track [here](https://documentation.freshpaint.io/developer-docs/freshpaint-android-sdk-reference#track).


### <a id="identify">

## Identify


When you `identify` a user, that user’s information is passed to AppsFlyer with `customer user Id` as AppsFlyer’s External User ID. Freshpaint’s special traits recognized as AppsFlyer’s standard user profile fields (in parentheses) are:

`customerUserId` (`Customer User Id`) <br>
`currencyCode` (`Currency Code`)

All other traits will be sent to AppsFlyer as custom attributes.

```java
Freshpaint freshpaint = Freshpaint.with(this);

freshpaint.identify("a user's id", new Traits()
.putName("a user's name")
.putEmail("maxim@appsflyer.com"),
null);
```

Check out the Freshpaint docs on indentify [here](https://documentation.freshpaint.io/developer-docs/freshpaint-android-sdk-reference#identify).

### <a id="conversion_data">

##  Get Conversion Data

For Conversion data your should call the method below.

```java
         AppsflyerIntegration.conversionListener  = new AppsflyerIntegration.ExternalAppsFlyerConversionListener() {
                    @Override
                    public void onConversionDataSuccess(Map<String, Object> map) {
                        // Process Deferred Deep Linking here
                        for (String attrName : map.keySet()) {
                            Log.d(TAG, "attribute: " + attrName + " = " + map.get(attrName));
                        }
                    }

                    @Override
                    public void onConversionDataFail(String s) {

                    }

                    @Override
                    public void onAppOpenAttribution(Map<String, String> map) {
                     // Process Direct Deep Linking here
                        for (String attrName : map.keySet()) {
                            Log.d(TAG, "attribute: " + attrName + " = " + map.get(attrName));
                        }
                    }

                    @Override
                    public void onAttributionFailure(String s) {

                    }
                };
```

In order for Conversion Data to be sent to Freshpaint, make sure you have enabled "Track Attribution Data" in AppsFlyer destination settings:

<img width="741" alt="Xnip2019-05-11_19-19-31" src="https://user-images.githubusercontent.com/18286267/57572409-8fb19200-7422-11e9-832f-fdd343af3137.png">

### <a id="deep_linking">

##  Unified deep linking
In order to implement unified deep linking, call the method below :

```java
        AppsflyerIntegration.deepLinkListener = new AppsflyerIntegration.ExternalDeepLinkListener() {
            @Override
            public void onDeepLinking(@NonNull DeepLinkResult deepLinkResult) {
                //TODO handle deep link logic
            }
        };
```
For more information about unified deep linking, check [here](https://dev.appsflyer.com/docs/android-unified-deep-linking)

