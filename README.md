##  LabCave Mediation Unity SDK
The current version (2.12.0) is compatible with Unity 5, iOS 9(Xcode 11) and above and Android 17 and above. It contains Android SDK 2.12.0 and iOS SDK 2.12.0. 
>**Xcode11 is mandatory for building the project**
 ## Adding Lab Cave Mediation SDK to your Project
Please follow these steps:
1. Download our SDK from the release section or clone it. 
>If you decide to clone it, you have to install [git LFS](https://git-lfs.github.com/) to clone it correctly, otherwise some ad networks will not be correctly integrated. 
2. Unzip the file and add all the unitypackages to your project by double clicking on it or by using the unity menu Assets/Import package/Custom Package and select our package. 
For iOS builds, please notice the mediation adds a post-proccess to configure the Xcode project properly in order to work with the mediation.
### Integrate Lab Cave Mediation Network Adapters
Add the providers (Ad networks) package you want to integrate as you did in the previous step. Some providers need some additional third party libraries but they are already included in their respective packages. **Make sure there are not duplicated libraries.**

**IMPORTANT IF YOU ARE USING ADMOB**

For **Android**, notice that we don't automatically add the AdMob SDK, as that packages is available natively on Android. You could be using it because some other SDK requires it. If you are not using it already in your project, you can add it using gradle or add the "play-services-ads.aar" that we provide in this repository. It is also mandatory to add the "consent-library-release.aar", also included in the LabCaveMediationAdMob.unitypackage file.
Finally, add these lines with your AdMob Application ID to your manifest file.
````java
 <meta-data
        android:name="com.google.android.gms.ads.APPLICATION_ID"
        android:value="YOUR_ADMOB_APPLICATION_ID"/>
````
In case of **iOS**, you have to add a *GADApplicationIdentifier* key with a string value of your AdMob app ID to your app's Info.plist file. You can find your App ID in the [AdMob dashboard](https://admob.google.com/home/).
````java
<key>GADApplicationIdentifier</key>
<string>YOUR_ADMOB_APP_ID</string>
````

>**IMPORTANT IF YOU ARE USING UNITYADS**
>
>You don't need to activate Unity Ads in the editor Unity Services window. Our SDK will add the correct Unity Ads SDK that works with the current version of our mediation. You can verify easily this in the Services panel and making sure that Ads module is unchecked.

>**IMPORTANT IF YOU ARE USING TAPJOY**
>
>You need to add the file named "Tapjoy.embeddedframework" to your XCode project, in XCode Files/Add files to ...

>**IMPORTANT IF YOU ARE BUILDING FOR IOS**
>
>If you are using pangle, applovin or fyber, these networks include bundle files (.bundle). Please add these files to your XCode project by Files/Add files to ...

## Initialize the SDK
The SDK Initialisation must be in a **Start** method. Avoid using it in an **Awake** method. The initialisation can be done in two ways:
1. Initialize each Ad Format separately at different points of the game. ***Recommended*** to minimise the number of ads preloaded without showing an impression.
> If you chose this option, make sure you don't call the *InitWithAppId* method following another Init. This might cause issues with initialisation.
You can bundle them in the same method
```cs
LabCaveMediation.InitWithAppHash("YOUR_API_HASH", this, LabCaveMediationAdFormats.INTERSTITIAL, LabCaveMediationAdFormats.BANNER)
/* Later stage in the game */
LabCaveMediation.InitWithAppHash("YOUR_API_HASH", this, LabCaveMediationAdFormats.REWARDED_VIDEO)
```
2. Alternatively, you can initiliase ALL of them at the same time with this method
```java
LabCaveMediation.InitWithAppHash("YOUR_API_HASH", this);
```
The appHash is the hash ID of your app, you can get it in https://mediation.labcavegames.com/panel/apps, "this" is the listener that will be called.
## SDK Listeners
The SDK offers a listener where you can receive the events of the ads.
```cs
    // Will be called when any ad is loaded, it will tell you the type LabCaveMediation.AdTypes.BANNER, LabCaveMediation.AdTypes.INSTERSTITIAL and LabCaveMediation.AdTypes.REWARDED_VIDEO
	public void OnMediationLoaded (LabCaveMediation.AdTypes adType)
	{
	}
	// When we received an error loading or showing an ad
	public void OnError (string description, LabCaveMediation.AdTypes type, string zoneId)
	{
	}
	// When an ad is clicked
	public void OnClick (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
	}
	// When an ad is closed
	public void OnClose (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
	}
	// When an ad is showed
	public void OnShow (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
		//AudioListener.pause = true;
	}
	 // When you have to give a reward after a rewarded-video
	public void OnReward (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
	}
```
## Showing Ads
Once you have correctly initialize the SDK and set the listeners, then you can show ads. 
>**The mediation SDK auto fetch all ads for you**, when you call the init method also will fetch the first ads, so you only need to call the show methods for the selected ad format.
You have to pass the ad placement where the ad will be shown "double-coins", "main-menu", "options", etc. It can also be an empty string but we recommend you to always define an ad placement. 
**The ad placements are automatically created on the dashboard and will appear after the first call of that specific ad placement is done.**
Make sure you check that the ad has been correctly loaded by calling the following methods:
```cs
LabCaveMediation.isBannerReady ();
LabCaveMediation.isInterstitialReady ();
LabCaveMediation.isRewardedVideoReady ();
```
Starting from SDK 2.10, you can also set capping for your ad locations in order to tweak your monetization. You can check if an ad location has reached its capping using this method:
```cs
LabCaveMediation.isAdLocationCapped (String "ad_location", LabCaveMediation.AdTypes adType );
```
## Interstitial
```cs
if(LabCaveMediation.isInterstitialReady ()){
	LabCaveMediation.ShowInterstitialWithZone ("PLACEMENT_IN_APP");
}
```
## Rewarded Video
```cs
if(LabCaveMediation.isRewardedVideoReady ()){
	LabCaveMediation.ShowVideoRewardedWithZone ("PLACEMENT_IN_APP");
}
```
## Banner
```cs
if(LabCaveMediation.isBannerReady ()){
	LabCaveMediation.ShowBannerWithZone ("PLACEMENT_IN_APP",  LabCaveMediationBannerSettings.BANNER_BOTTOM);
}
```
The position **TOP** or **BOTTOM** and the size SMART(SCREEN_SIZEx50) or BANNER (320x50) can be set at the beginning of the execution or when you call "showBanner":
```cs
LabCaveMediation.ShowBannerWithZone ("PLACEMENT_IN_APP",  LabCaveMediationBannerSettings.SMART_TOP);
LabCaveMediation.ShowBannerWithZone ("PLACEMENT_IN_APP",  LabCaveMediationBannerSettings.SMART_BOTOM);
LabCaveMediation.ShowBannerWithZone ("PLACEMENT_IN_APP",  LabCaveMediationBannerSettings.BANNER_TOP);
LabCaveMediation.ShowBannerWithZone ("PLACEMENT_IN_APP",  LabCaveMediationBannerSettings.BANNER_BOTTOM);
```
**If you don't define any, it defaults to "SMART_BOTTOM"**

It's important to control the banner behavior on Android, otherwise you might be rendering impressions while the application is not in the foreground.
When your application is brought to the background, you should call "HideBanner". When is brought into the foreground again, you may use "ShowBannerWithZone" to show the banner again.

### Verify the integration
In order to check if the SDK is correct, open the test module, you have to call the "Init" method first and wait till the "onInit" listener method is called:
```cs
LabCaveMediation.InitTest ("YOUR_API_HASH");
```
>**Make sure you remove this test module on your release build.**
## Advance integration
### Debugging
You can enable logging to get additional information by using the following method:
```java
LabCaveMediation.setLogging(true);
```
### GDPR
>Make sure you set the GDPR user consent before initiliasing the Mediation.
You can set the user consent to the SDK if you manage it. If you don't, the mediation will ask the user for the consent. 
You can use the following methods:
```java
LabCaveMediation.SetConsentStatus(true);
```
### IAPs Event (SDK 2.12 and above)
>We offer the following optional methods to record purchase events (and associate revenue to them) and calculate your LTV (Ads + IAPs). Make sure you only send us IAPs that have been correctly validated, otherwise the LTV calculation might get affected. This feature is completely independent of the ads integration.
```java
public static void PurchaseEvent(float revenue, string currency, string productId, string orderId){}
   
public static void PurchaseEvent(float revenue, string currency, string productId, int quantity, string orderId){}
```

You should send us the parameters following this format:
* Revenue: The total revenue from that in-app purchase. The revenue value should not contain comma separators, currency sign, or text. A revenue event should be similar to 1234.56, for example.
* Currency: You should send the currency using the [3 character ISO 4217 code](https://en.wikipedia.org/wiki/ISO_4217#Active_codes). For example: USD or EUR
* ProductId: You can use here the bundle id of the product. For example: "com.game.bagofcoins"
* Quantity: The number of items bought. It defaults to 1
* OrderId: The confirmation ID from store in order to avoid event duplications.
