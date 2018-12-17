---
description: This page will get you know how to put your app widget onto Shelf
---

# Instruction to Engage with OnePlus Launcher Shelf

## External Widget Provider - manage your App Widget on _Shelf_

The `External Widget Provider` is a _ContentProvider_ built by OnePlus Launcher team for authorized external applications to manage their _App widgets_ in Shelf, the application owner requires to apply for an _API key_ with corresponding _ComponentName_ of the App widget.  

#### **content provider URI**

> content://net.oneplus.launcher.shelf/widgets

#### **required permission**

> net.oneplus.launcher.permission.EXTERNAL\_WIDGET

#### **method**

> manageWidget

**supported arguments**

> * available
> * add
> * remove

#### sample code

```java
  final ContentResolver resolver = getContext().getContentResolver();
  final Uri EXTERNAL_WIDGET_URI = Uri.parse("content://net.oneplus.launcher.shelf/widgets");

  Bundle bundle = new Bundle()
  bundle.putString("package", getApplicationContext().getPackageName());
  bundle.putString("class", MyAppWidgetProvider.class.getName());

  // check if Launcher supports external widget management
  Bundle queryResult = resolver.call(EXTERNAL_WIDGET_URI, "manageWidget", "available", bundle);
  if (queryResult == null) {
      Log.e("Test", "the method is not available");
  }

  // check if Shelf accepts new app widget to be added
  boolean isAvailable = queryResult.getBoolean("seat_available", false);
  if (!isAvailable) {
      Log.e("Test", "there is no seat for new widget any more!");
  }

  // add new app widget onto shelf
  Bundle addResult = resolver.call(EXTERNAL_WIDGET_URI, "manageWidget", "add", bundle);
  if (addResult == null) {
      Log.e("Test", "the method is not available");
  }

  int widgetId = addResult.getInt("widget_id", -1);
  boolean isReachedLimit = addResult.getBoolean("reach_limit", false);
  if (widgetId < 0) {
      Log.e("Test", "add widget failed");
      if (isReachedLimit) {
          Log.e("Test", "the number of widgets in Shelf has reached the limitation");
      }
  } else {
      // do something to save the mapping of the node id and widget id
  }

  // remove the existing app widget from Shelf (reuse the previous configured bundle object)
  bundle.putInt("widget_id", widgetId); // the widget id of the corresponding widget to be removed
  bundle.putBoolean("external_only", true); // only remove the widget which is not added from shelf
  Bundle removeResult = resolver.call(EXTERNAL_WIDGET_URI, "manageWidget", "remove", bundle);
  if (removeResult == null) {
      Log.e("Test", "the method is not available");
  }

  boolean isSuccess = removeResult.getBoolean("success", false);
  if (!isSuccess) {
      Log.e("Test", "remove widget from Shelf failed with id: " + widgetId);
  }
```

### Request permission to access External Widget Provider

add `net.oneplus.launcher.permission.EXTERNAL_WIDGET` permission in your app's _AndroidManifest.xml_

```xml
  <?xml version="1.0" encoding="utf-8"?>
  <manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.example.myapplication" >
      <uses-permission android:name="net.oneplus.launcher.permission.EXTERNAL_WIDGET" />
      <application
        <!-- other contents -->
      </application>
  </manifest>
```

### Arguments description

**add - to add an app widget on the Shelf**

**Input extras**

| **Key** | **Value** |
| :--- | :--- |
| package | the **package name** of the widget component to be bound |
| class | the **class name** of the widget component to be bound |
| api_key | the corresponding **API key** for managing the widget component |

**Output extras**

| **Key** | **Value** |
| :--- | :--- |
| widget\_id | the id of the bound widget, _-1_ given if the widget binding failed |
| reach\_limit | _true_ if the number of widgets in _Shelf_ has reached the limitation |

**remove - to remove an app widget from Shelf**

**Input extras**

| **Key** | **Value** |
| :--- | :--- |
| package | the **package name** of the widget component to be removed |
| class | the **class name** of the widget component to be removed |
| api_key | the corresponding **API key** for managing the widget component |
| widget\_id | the id of the widget component to be removed |
| external\_only | only remove the widget component which was added externally |

**Output extras**

| **Key** | **Value** |
| :--- | :--- |
| widget\_id | the id of the removed widget component |
| success | _true_ if the widget component was successfully removed from _Shelf_ |

**available - check if Shelf accepts new app widget to be added**

**Input extras**

| **Key** | **Value** |
| :--- | :--- |
| package | the **package name** of the widget component used to query _Shelf_ status |
| class | the **class name** of the widget component used to query _Shelf_ status |
| api_key | the corresponding **API key** for managing the widget component |

**Output extras**

| **Key** | **Value** |
| :--- | :--- |
| seat\_available | _true_ if there is enough seats for new App widget in _Shelf_ |

