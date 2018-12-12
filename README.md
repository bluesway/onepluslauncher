![OnePlus Launcher](https://lh3.googleusercontent.com/b-40YX8xbQsPxOVsPWchsaNRv5cJBqvF0jVGNHBsllUTURVQ8EGmCVsMy1RadIpXuekg=s240)

Instruction to Engage with OnePlus Launcher Shelf
=============================
![MarkDown](https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/64px-Markdown-mark.svg.png)
###### IMPORTANT: This document is written with former MarkDown syntax, reading with a MarkDown viewer is best recommended

# External Widget Provider - manage your App Widget on *Shelf*

OnePlus Launcher built a *ContentProvider* for authorized external applicaitions to add/remove their *App Widgets* onto/from Shelf.  
We called it `External Widget Provider`

Guidance
--------
#### content provider URI
> content://net.oneplus.launcher.shelf/widgets

#### required permission
> net.oneplus.launcher.permission.EXTERNAL_WIDGET

#### method
> manageWidget

#### supported arguments
> * available
> * add
> * remove

### sample code

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
  Bundle removeResult = resolver.call(SHELF_URI, "manageWidget", "remove", bundle);
  if (removeResult == null) {
      Log.e("Test", "the method is not available");
  }
  
  boolean isSuccess = removeResult.getBoolean("success", false);
  if (!isSuccess) {
      Log.e("Test", "remove widget from Shelf failed with id: " + widgetId);
  }
  ```
### Grant permission to access Shelf
add `net.oneplus.launcher.permission.EXTERNAL_WIDGET` in your app's *AndroidManifest.xml*

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.example.myapplication" >
      <uses-permission android:name="net.oneplus.launcher.permission.EXTERNAL_WIDGET" />
      <application
        <!-- other contents -->
      </application>
  </manifest>
  ```

### API instruction
#### add - to add an app widget on the *Shelf*
###### Input extras
| **Key**        | **Value**                                                                  |
|----------------|----------------------------------------------------------------------------|
| package        | the **package name** for the widget component to be bound                  |
| class          | the **class name** for the widget component to be bound                    |

###### Output extras
| **Key**        | **Value**                                                                  |
|----------------|----------------------------------------------------------------------------|
| widget_id      | the id of the bound widget, *-1* given if the widget binding failed        |
| reach_limit    | *true* if the number of widgets in *Shelf* has reached the limitation      |

#### remove - to remove an app widget from *Shelf*
###### Input extras
| **Key**        | **Value**                                                                  |
|----------------|----------------------------------------------------------------------------|
| package        | the **package name** for the widget component to be removed                |
| class          | the **class name** for the widget component to be removed                  |
| widget_id      | the id of the widget component to be removed                               |
| external_only  | only remove the widget component which was added externally                | 

###### Output extras
| **Key**        | **Value**                                                                  |
|----------------|----------------------------------------------------------------------------|
| widget_id      | the id of the removed widget component                                     |
| success        | *true* if the widget component was successfully removed from *Shelf*       |

#### available - check if *Shelf* accepts new app widget to be added
###### Input extras
| **Key**        | **Value**                                                                  |
|----------------|----------------------------------------------------------------------------|
| package        | the **package name** for the widget component used to query *Shelf* status |
| class          | the **class name** for the widget component used to query *Shelf* status   |

###### Output extras
| **Key**        | **Value**                                                                  |
|----------------|----------------------------------------------------------------------------|
| seat_available | *true* if there is still available seats for new app widget in *Shelf*     |

## Limitation
* to prevent malicious applicaiton messed up user's *Shelf*, before engeging with the External Widget Provider, application owner requires to notify Launcher team to unlock the restriction for your application's package name

# Implement *General Card* with Shelf SDK
Normal Card
-----------
### Description
### Getting started
#### Write a CardProvider
#### Update the card with specific style on callback received

VIP Card
--------
### Description
### Getting started
#### Write a CardProvider
#### Create the card with specific style
#### Post the card onto Shelf
