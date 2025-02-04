## conviva-android-appanalytics

Use Application Analytics to autocollect events and track application specific events and state changes.

## Download

You can download .aar from GitHub's [releases page](https://github.com/bumptech/glide/releases).

Add the following line to app's **build.gradle** file along with the dependencies:

```plaintext
dependencies {
    ...
    implementation 'com.conviva.sdk:conviva-android-tracker:<version>'

    // Conviva video sensor dependency(recommended from 4.0.30 and above excluding 4.0.31)
    implementation 'com.conviva.sdk:conviva-core-sdk:<version>'
    ...
}
```

Add the plugin

```plaintext
// in the root or project-level build.gradle
dependencies {
    ...
    // Unified Conviva Plugin supported from 0.3.5 onwards, use
    classpath 'com.conviva.sdk:android-plugin:0.3.x'
    ...
}

    // in the app, build.gradle at the end of plugins add the
    ...
    apply plugin: 'com.conviva.sdk.android-plugin'



// in the app, build.gradle.kts at the end of plugins add the
plugins {
    id 'com.conviva.sdk.android-plugin'
}
```

Check the compatible plugin version from [here](https://github.com/Conviva/conviva-android-plugin).

## Proguard rules

Please add the following proguard rules to keep conviva sdk classes from obfuscation.

```plaintext
-keep class com.conviva.** { *; }
```

## Multidex Config

If multidex is enabled and a multidex-config.pro is being used by the application, please add the following rule to the config.pro file.

```plaintext
-keep class com.conviva.** { *; }
```

## Support Android Version

Target sdk version : Android 14 (API level 34)

Minimum sdk version : Android 5.0 (API level 21)

## Initialize the tracker by enabling autocollection

```plaintext
TrackerController tracker = ConvivaAppAnalytics.createTracker(context,
    customerKey,
    appName
);

// The tracker object can be fetched using the following API in the other classes
// then the place where createTracker is invoked using following API:
TrackerController tracker = ConvivaAppAnalytics.getTracker();
```

**customerKey** - a string to identify specific customer account. Different keys shall be used for development / debug versus production environment. Find your keys on the account info page in Pulse.

**appName** - a string value used to distinguish your applications. Simple values that are unique across all of your integrated platforms work best here.

#### Note : It is recommended to initialize the tracker at the start of the application before the first activity class.

## Set the user id (viewer id)

```plaintext
tracker.getSubject().setUserId(userId);
```

## Extend tracking to track your application specific events and state changes

Use **trackCustomEvent()** API to track all kinds of events. This API provides 2 fields to describe the tracked events:

**eventName** - Name of the custom event

**eventData** - Any type of data in JSONObject format

The following example shows the implementation of the 'onClick' event listener to any element.

```plaintext

JSONObject eventDataJSON = new JSONObject();
eventDataJSON.put("identifier1", intValue);
eventDataJSON.put("identifier2", boolValue);
eventDataJSON.put("identifier3", "stringValue");

String eventName = "your-event-name";

tracker.trackCustomEvent(eventName, eventDataJSON);
```

**track application with data in JSON String format**

## trackCustomEvent() with data in JSON String format

Use **trackCustomEvent()** API to track all kinds of events. This API provides 2 fields to describe the tracked events:

**eventName** - Name of the custom event

**eventData** - Any type of data in JSON String format

The following example shows the implementation of the 'onClick' event listener to any element.

```plaintext
// ... send events 'onClick' of button
HashMap<String, Object> eventData = new HashMap<>(); 
eventData.put("identifier1", intValue); 
eventData.put("identifier2", boolValue); 
eventData.put("identifier3", "stringValue");

String eventName = "your-event-name";

tracker.trackCustomEvent(eventName, JSONValue.toJSONString(eventData));
```

## Extend tracking to set your application specific custom tags

Use **setCustomTags()** API to set custom tags

Use **clearCustomTags()** API to clear few of the previously set custom tags

Use **clearAllCustomTags()** API to clear all the previously set custom tags  
User Clicks detection

The following example shows the implementation of the application using these API's:

```plaintext
// Adds the custom tags
HashMap<String, Object> tags = new HashMap<>(); 
eventData.put("key1", intValue); 
eventData.put("key2", boolValue); Refer limitations
eventData.put("key3", "stringValue");
tracker.setCustomTags(tags);

// clears few of the custom tags
Set<String> clearTagKeysSet = new HashSet<>();
clearTagKeysSet.add("key1"); 
clearTagKeysSet.add("key2"); 
tracker.clearCustomTags(clearTagKeysSet);

// clears all the custom tags
tracker.clearAllCustomTags();
```

### Enable Traceparent Header generation and collection

Please contact conviva representative to enable this feature.

## API to override the default Activity Name in the Screen View Event

This feature supports overriding the default Activity Name in the Screen View Event. Add the public variable _convivaScreenName_ in the corresponding activity which you want to set the screen name supported from 0.9.0 version onwards

The following example shows how to include the plugin:

```plaintext
public class ExampleActivity extends Activity {
    ...
    public String convivaScreenName = "HomeScreen";
    ...
```

**Auto-collected Events**

##### Conviva provides a rich set of application performance metrics with the help of auto collected app events. Below are the events which are auto collected once above initialisation is done.

| Event | Occurrence |
| --- | --- |
| network\_request | after receiving the network request response [Refer limitations](#Limitations) |
| screen\_view | when the screen is interacted on either first launch or relaunch. [Refer limitations](#Limitations) |
| application\_error | when an error occurrs in the application |
| button\_click | on the button click callback (works both Clickable Views and Clickable Modifiers in compose) |
| application\_background | when the application is taken to the background |
| application\_foreground | when the application is taken to the foreground |
| application\_install | when the application is launched for the first time after it's installed. (It's not the exact installed time.)[Refer limitations](#Limitations) |
| deep\_link\_received | on opening an application using the UTM URL |
| anr\_start | Timer starts for the response from the main thread. If it takes more than 4 seconds, _anr\_start_ event is triggered. |
| anr\_end | If the SDK gets response after triggering _anr\_start_, then _anr\_end_ is dispatched. |
| conviva\_fragment\_view | Whenever a fragment transaction commits |
| conviva\_compose\_view | Whenever a destination change occurs in the NavController of the ComposeNavigation |

### Limitations:

*   Starting from version [v0.9.7](https://github.com/Conviva/conviva-android-appanalytics/releases/tag/v0.9.7), the auto-collection of **screen\_view** and **application\_install** events is temporarily affected due to controlled ingestion by Conviva. This impact occurs only during the first fresh launch after an app install or clear-data. It is valid only until the Conviva Remote Config becomes available and will no longer persist in subsequent launches.
*   **Request and Response Body Collection**:
    *   Collected only when:
        *   Size is \< 10KB and content-length is available.
        *   Content-type is `"json"` or `"text/plain"`.
        *   Data is a `JSONObject`, nested `JSONObject`, or `JSONArray`.
*   **Request and Response Header Collection**:
    *   Collected only when:
        *   Data is a `JSONObject` (nested `JSONObject` and `JSONArray` are not yet supported).
        *   The server is provisioned with `"Access-Control-Expose-Headers:"`.

To learn about the default metrics for analyzing the native and web applications performance, such as App Crashes, Avg Screen Load Time, and Page Loads, refer to the [App Experience Metrics](https://pulse.conviva.com/learning-center/content/eco/eco_metrics.html) page in the Learning Center.
