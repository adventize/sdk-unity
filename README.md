# Adventize Unity SDK integration

To integrate your Unity app with Adventize services you'll need to go to [adventize.com](https://adventize.com) and create Adventize application for every platform you'll compiled your app to. Each Adventize application has a pair `appId`/`secret` which will need you in integration process.

Then download [adventize-unity-sdk.tar.gz](https://github.com/adventize/sdk-unity/raw/master/adventize-unity-sdk.tar.gz). In archive you'll find a file `adventize-sdk.unitypackage`. Package contains following items:
  <br/>&nbsp;&nbsp;&nbsp;&nbsp;`AdventizeSDK` -- Adventize SDK class
  <br/>&nbsp;&nbsp;&nbsp;&nbsp;`Plugins/Android` -- Android related libraries
  <br/>&nbsp;&nbsp;&nbsp;&nbsp;`Plugins/iOS` -- iOS related libraries
  <br/>&nbsp;&nbsp;&nbsp;&nbsp;`Example` -- AdventizeSDK class usage examples.

Import package into your Unity project (in main menu: *Assets* > *Import Package* > *Custom package...* > Choose file `adventize-sdk.unitypackage`).

Well, let's start integration.

### Integrate your app as advertiser

To acquire status "Advertiser" for your application you must teach it to send sessions to Adventize server on application start event. Your application must send at least three sessions before status will be accepted.

Here is a short manual:

1. First you must create instance of AdventizeSDK class:

        AdventizeSDK adventize = new AdventizeSDK("your appId", "your secret");
    
    > **Important!** If you want to compile your app on more than one platforms, then you'll need to specify pairs of          `appId`/`secret` for all platforms.  For example:

        #if UNITY_ANDROID
            AdventizeSDK adventize = new AdventizeSDK("your appId for Android", "your secret for Android");
        #elif UNITY_IPHONE
            AdventizeSDK adventize = new AdventizeSDK("your appId for iOS", "your secret for iOS");
        #endif
    
2. Then paste following code in your application on-start handler:
    
        adventize.startSession();

3. If you planning to run CPA (Cost per action) advertising campaign for your application, use `sendAction` method in point place of your app to send action compete fact:

        adventize.sendAction("action identifier");

  Also SDK may track application stop event via Google Analitycs plugin. Paste this code in application's on-stop handler:

        adventize.stopSession();
    
That's all. Now complie your application and start & stop it for three times. If everything is correct, you'll see that your application acquire "Advertiser" status at [adventize.com](https://adventize.com).

### Integrate your app as publisher

To become "Publisher" your application must correctly fetch advertisement data from Adventize servers. After three fetches your app will acquire status "Publisher".

1. Make instance of AdventizeSDK class like illustrated in manual above.
2. To fetch ad you need to specify *location(s)* which you want use to show ad.

    > For each Adventize application (at [adventize.com](https://adventize.com)) in publisher's section you may create a *location*. *Location* is a place in your application where advertisement will be shown. Each *location* has a `alias` property, use it to identify *locations* in your code.
    
    Creating locations:

        AdventizeSDK.Location location = new AdventizeSDK.Location("alias", x, y, with, height);
        
        // or you may use another constructor
        AdventizeSDK.Location location = new AdventizeSDK.Location("alias");
        
    Feching ad into location "offerwall":
    
        AdventizeSDK.Location[] locations = new AdventizeSDK.Location[n];
        locations [0] = new AdventizeSDK.Location ("offerwall");
        
        adventize.askForBanners(locations);
        
3. Also with fetch request you may send additional data of your application:

        // specifing application's user ID
        adventize.setUserId("your user id");

        // specifing user's rate-related information
        adventize.setUserRate("user's in-game balance");
        
4. To complete integration compile your app and make three advertisement fetches.

## Platform specific instructions

### iOS troubleshooting

As some versions of Unity have troubles with generating proper Xcode project - you should fix them manualy in some cases. The thing is Unity doesn't turn on Objective-C Exceptions. To turn in on go to:

    Project Navigator > Unity-iPhone (target) -> build settings -> Enable Objective-C Exceptions

Adventize Unity SDK also uses some specific Frameworks, which are not included in Unity Xcode default generated project. So, after opening generated Xcode project you should add some Frameworks to it:

    Unity-iPhone -> Unity-iPhone (target) -> Build Phases -> Link Binary With Libraries
Add `AdSupport`, `libz.dylib`, `CoreData`.

###Android troubleshooting

Add these permissions to AndroidManifest.xml file if you don't have it already:

    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />


