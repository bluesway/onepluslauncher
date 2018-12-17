---
description: This page briefly introduces how to work with Shelf SDK
---

# Implement General Card with Shelf SDK

OnePlus Launcher provided a public SDK called **Shelf SDK**, application can use it to generate stylish **General Cards** on _Shelf_, please ask the SDK library \(ShelfLib-release.jar\) from Launcher team

**API Documentation**

You can check the **Shelf SDK** documentation in Javadoc format [here](http://blog.bluesway.org/onepluslauncher/) \(this is just a temporary site, we may find a permanent place to host the document in the future\)

## Getting started

The content provider for a **General Card** is actually a formal _BroadcastReceiver_, there are several configuration you have to completed before implementing the **Shelf SDK**

### **Request permission to access General Card manager**

add `net.oneplus.shelf.permission.CARD` permission in your app's _AndroidManifest.xml_

```markup
  <?xml version="1.0" encoding="utf-8"?>
  <manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.example.myapplication" >
      <uses-permission android:name="net.oneplus.shelf.permission.CARD" />
      <application
        <!-- other contents -->
      </application>
  </manifest>
```

and assign the same permission to your BroadcastReceiver component's section in your app's _AndroidManifest.xml_

### **Configure intent filter**

Tou need to add following intent filters to receive corresponding events:

* net.oneplus.shelf.action.CARD\_UPDATE
* net.oneplus.shelf.action.CARD\_DELETED
* net.oneplus.shelf.action.ACTION\_POSITIVE
* net.oneplus.shelf.action.ACTION\_NEGATIVE

### **Configure meta data for the General Card**

To present your card to user, you need to provide a name to the General Card and a preview image to show a snapshot for your content; you need to configure the data with resource ID.

| data name | resource |
| :--- | :--- |
| net.oneplus.shelf.card.name | the string reference to the card name |
| net.oneplus.shelf.card.preview | the drawable reference to the preview image. |

### **An example for declaring a General Card component in the manifest**

```markup
<receiver
    android:name=".DemoCardProvider"
    android:icon="@drawable/ic_demo"
    android:label="@string/provider_name_demo"
    android:permission="net.oneplus.shelf.permission.CARD">

    <intent-filter>
        <action android:name="net.oneplus.shelf.action.CARD_UPDATE"/>
        <action android:name="net.oneplus.shelf.action.CARD_DELETED"/>
        <action android:name="net.oneplus.shelf.action.ACTION_POSITIVE"/>
        <action android:name="net.oneplus.shelf.action.ACTION_NEGATIVE"/>
    </intent-filter>

    <meta-data
        android:name="net.oneplus.shelf.card.preview"
        android:resource="@drawable/ic_demo"/>
    <meta-data
        android:name="net.oneplus.shelf.card.name"
        android:resource="@string/demo_card_name"/>
</receiver>
```

### **Card actions**

You can define the actions to perform when user clicks​ on cards or buttons.  
Action capabilities:​

* Starting an activity​
* Starting a service​
* Delivering a broadcast

Check the API documentation for following content:

* Card.Builder.setPrimaryAction\(Card.Action\)
* Card.Builder.setPositiveAction\(Card.Action\)
* Card.Builder.setNegativeAction\(Card.Action\)
* Card.Builder.setNeutralAction\(Card.Action\)

### **Card images**

The images used for the **General Card** are uploaded to launcher through file provider, **Shelf SDK** provided a wrapper class to store your images, here's the supported image source:

* Resource \(R.drawable.sample\_image\)
* Drawable \(android.graphics.drawable.Drawable\)
* Bitmap \(android.graphics.Bitmap\)
* URL \([https://com.example.image/demo.png](https://com.example.image.png)\)
* Uri \(content://image.provider.get/2\)

Check the API documentation for `net.oneplus.shelf.card.Image`

### **Card styles**

Launcher pre-designed several card styles for general purpose

#### **TextContentStyle for presenting the pure text content**

![TextContentStyle](https://raw.githubusercontent.com/bluesway/onepluslauncher/master/images/TextContentStyle.png)

```java
import net.oneplus.shelf.card.Card;
import net.oneplus.shelf.card.TextContentStyle;

Card.Style style = new TextContentStyle().setSubtitle(mSubtitle).setContent(mContent);
```

#### **ListViewStyle for presenting a list of items**

![ListStyle1](https://raw.githubusercontent.com/bluesway/onepluslauncher/master/images/ListStyle1.png) ![ListStyle2](https://raw.githubusercontent.com/bluesway/onepluslauncher/master/images/ListStyle2.png)

```java
import net.oneplus.shelf.card.Card;
import net.oneplus.shelf.card.ListViewStyle;
import net.oneplus.shelf.card.ListViewStyle.Item;

ListViewStyle.Item item1 = new ListViewStyle.Item(mSubtitle, mContent);
ListViewStyle.Item item2 = new ListViewStyle.Item(image, mSubtitle, mContent, null);
ListViewStyle.Item item3 = new ListViewStyle.Item(mSubtitle, Card.Action.newActivity(reminderIntent));
ListViewStyle.Item item4 = new ListViewStyle.Item(image, mSubtitle, null, Card.Action.newActivity(reminderIntent));
ListViewStyle.Item item5 = new ListViewStyle.Item(mSubtitle);
ListViewStyle.Item item6 = new ListViewStyle.Item(mSubtitle, mContent, Card.Action.newActivity(reminderIntent));
Card.Style style = new ListViewStyle().addItem(item1).addItem(item2).addItem(item3).addItem(item4).addItem(item5).addItem(item6);
```

#### **ImageContentStyle for presenting an image with text description, with different layouts**

![SmallImageStyle](https://raw.githubusercontent.com/bluesway/onepluslauncher/master/images/SmallImageStyle.png) ![MiddleImageStyle](https://raw.githubusercontent.com/bluesway/onepluslauncher/master/images/MiddleImageStyle.png) ![HugeImageStyle1](https://raw.githubusercontent.com/bluesway/onepluslauncher/master/images/HugeImageStyle1.png) ![HugeImageStyle2](https://raw.githubusercontent.com/bluesway/onepluslauncher/master/images/HugeImageStyle2.png)

```java
import net.oneplus.shelf.card.Card;
import net.oneplus.shelf.card.ImageContentStyle;
import net.oneplus.shelf.card.ImageContentStyle.ImageStyle.MEDIUM;

Card.Style style = new ImageContentStyle().setImage(image)
        .setImageStyle(ImageContentStyle.ImageStyle.MEDIUM)
        .setSubtitle(mSubtitle)
        .setContent(mContent);
```

#### **CustomStyle for presenting a customized layout provided by yourself**

![CustomStyle](https://raw.githubusercontent.com/bluesway/onepluslauncher/master/images/CustomStyle.png)

```java
CardStyle style = new CustomStyle()
        .setRootLayout(R.layout.custom_card)
        .setMargins(R.id.custom_card_title,
                getResources().getDimensionPixelSize(R.dimen.custom_card_general_margin_top),
                getResources().getDimensionPixelSize(R.dimen.custom_card_general_margin_start),
                getResources().getDimensionPixelSize(R.dimen.custom_card_general_margin_end),
                0)
        .setMargins(R.id.custom_card_subtitle,
                getResources().getDimensionPixelSize(R.dimen.custom_card_general_margin_top),
                getResources().getDimensionPixelSize(R.dimen.custom_card_general_margin_start),
                getResources().getDimensionPixelSize(R.dimen.custom_card_general_margin_end),
                getResources().getDimensionPixelSize(R.dimen.custom_card_general_margin_bottom))
        .setMargins(R.id.custom_card_icon, 0, 0, getResources().getDimensionPixelSize(R.dimen.custom_card_general_inner_margin), 0)
        .setTextView(R.id.custom_card_title, "::CUSTOM:: " + mSubtitle, getResources().getDimensionPixelSize(R.dimen.oneplus_contorl_text_size_subtitle))
        .setTextFont(R.id.custom_card_title, getString(R.string.oneplus_contorl_font_family_subtitle))
        .setTextView(R.id.custom_card_subtitle, mContent, getResources().getDimensionPixelSize(R.dimen.oneplus_contorl_text_size_body1))
        .setTextFont(R.id.custom_card_subtitle, getString(R.string.oneplus_contorl_font_family_subtitle))
        .setImageView(R.id.custom_card_icon, icon)
        .setImageView(R.id.custom_card_image, image);
```

### **Normal Card**

A **Normal Card** is a public type of **General Card**, which requires no specific authorization, application can build its own **Normal Cards** and they will appear in the _Shelf_ item picker for user to add it onto _Shelf_ manually.

#### **Write a CardProvider for Normal Card**

A handy helper class for you to implement card provider is provided, all you need to do is just extend the class `net.oneplus.shelf.card.CardProvider`

In common cases you need to prepare the updated content for your card when you receive _onUpdate\(\)_ callback, and use the parameters to construct the Card.Style object, and finally generate a new Card instance and post it on _Shelf_ to update the existing card.

See the sample implementation below

```java
import net.oneplus.shelf.card.Card;
import net.oneplus.shelf.card.CardManager;
import net.oneplus.shelf.card.CardProvider;
import net.oneplus.shelf.card.Image;
import net.oneplus.shelf.card.ListViewStyle;
import net.oneplus.shelf.card.result.PostCardResult;


public class DemoCardProvider extends CardProvider {
    private static final String TAG = DemoCardProvider.class.getSimpleName();

    @Override
    public void onUpdate(Context context, CardManager cardManager, String channelToken, int tag) {
        // prepare the card style
        PackageManager pm = context.getPackageManager();
        ListViewStyle listStyle = new ListViewStyle();
        String[] pkgList = new String[]{"com.example.package1", "com.example.package2", "com.example.package3"};
        int[] iconList = new int[] {R.drawable.ic_item1, R.drawable.ic_item2, R.drawable.ic_item3}

        for (int i = 0; i < pkgList.length; i++) {
            String app = pkgList[i];
            try {
                Image icon = new Image(context.getResources().getDrawable(R.drawable.ic_item[i]));
                String name = info.loadLabel(pm).toString();
                Intent intent = pm.getLaunchIntentForPackage(app);
                Card.Action action = Card.Action.newActivity(intent);

                listStyle.addItem(new ListViewStyle.Item(icon, name, app, action));
            } catch (PackageManager.NameNotFoundException e) {
                Log.e(TAG, "cannot find the package on the device:" + app, e);
            }
        }

        Image icon = new Image(context.getDrawable(R.drawable.ic_demo));
        String name = context.getString(R.string.app_name);
        Intent intent = new Intent(context, DemoActivity.class);
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TASK);
        Card.Action action = Card.Action.newActivity(intent);
        listStyle.addItem(new ListViewStyle.Item(icon, name,
                DemoActivity.class.getCanonicalName(), action));

        // generate the new card
        Card card = new Card.Builder(listStyle).setRefreshable(true)
                .setTitle(context.getString(R.string.demo_card_title)).build();

        // post to Shelf
        PostCardResult result = cardManager.post(channelToken, card);
        if (!result.isSuccess()) {
            Log.e(TAG, result.getMessage());
        }
    }

    @Override
    public void onDeleted(Context context, String channelToken, int tag) {
        String alert = "Card " + context.getString(R.string.demo_card_title
        ) +
                " deleted!\nToken: " + channelToken + "\nTag: " + tag;
        Toast.makeText(context, alert, Toast.LENGTH_LONG).show();
    }

    @Override
    public void onReceive(Context context, Intent intent) {
        super.onReceive(context, intent);

        String action = intent.getAction();
        Log.i(TAG, "onReceive: action=" + action);
    }
}
```

### **VIP Card**

A **VIP Card** is a private type of **General Card**, which requires to apply for an _API Key_ with corresponding _Channel Token_ from Launcher team, application can implement its own **VIP Cards** and publish or remove the card to/from _Shelf_ **directly** without user's operation.

#### **Special meta data for VIP Card**

You need to append the extra meta data for the _BroadcastReceiver_ component with the given _API Key_

| data name | value |
| :--- | :--- |
| net.oneplus.shelf.card.api\_key | the raw string of the given API Key. |

```markup
<receiver
    ...
    <meta-data
        android:name="net.oneplus.shelf.card.api_key"
        android:value="Ptr/J0kQ9jxZ23HMvuio76asdfkjG5/TcV9JietuUanKzHFWjK3l120jvbHAH002Pl"/>
    ...
</receiver>
```

#### **Register the VIP Channel**

Before you are going to post a **VIP card** onto _Shelf_, you need to register your _channel token_ first

```java
final Context context = getActivity().getApplicationContext();
final String apiKey = "Ptr/J0kQ9jxZ23HMvuio76asdfkjG5/TcV9JietuUanKzHFWjK3l120jvbHAH002Pl";
Image icon = mIconImageBitmap != null ? new Image(mIconImageBitmap) :
        new Image(getResources(), mIconImageResId);

final ChannelManager channelManager = ChannelManager.getInstance(context);
IsChannelRegisteredResult result = channelManager.isChannelRegistered(mChannelToken);
if (!result.isRegistered()) {
    ComponentName provider = new ComponentName(context, DemoCardProvider.class);
    Channel channel = new Channel(mChannelToken, icon, provider);
    RegisterChannelResult registerResult = channelManager.registerChannel(apiKey, channel);
    if (!registerResult.isSuccess()) {
        Log.e(TAG, "Failed to register channel of token: " + mChannelToken);
        return false;
    }
} else {
    ComponentName provider = new ComponentName(context, DemoCardProvider.class);
    Channel channel = new Channel(mChannelToken, icon, provider);
    RegisterChannelResult registerResult = channelManager.registerChannel(apiKey, channel);
    if (!registerResult.isSuccess()) {
        Log.e(TAG, "Failed to update channel (token: " + mChannelToken + "): " +
        registerResult.getMessage());
        return false;
    }
}
```

Check the API documentation for `net.oneplus.shelf.card.ChannelManager`

#### **Post the card onto Shelf**

There is one thing different for posting **Normal Card** and **VIP Card**, whilst you used `cardManager.post(channelToken, card);` to post a card on _Shelf_ for **Normal Card**, you will use following overloaded method for posting a **VIP Card**  
`CardManager.getInstance(context).post(apiKey, mChannelToken, CardManager.TAG_PRIMARY, card)`

Check the API documentation for `net.oneplus.shelf.card.CardManager`

