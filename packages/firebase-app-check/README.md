# @nativescript/firebase-app-check
A plugin that allows you to add [Firebase App Check](https://firebase.google.com/docs/app-check) to your NativeScript app. 

> **Note:** Use this plugin with the [@nativescript/firebase-core](../firebase-core/) plugin to initialize Firebase in your app.

[![image](https://img.youtube.com/vi/Fjj4fmr2t04/hqdefault.jpg)](https://www.youtube.com/watch?v=Fjj4fmr2t04)

The plugin supports the following services as attestation providers:

- [DeviceCheck on iOS](https://firebase.google.com/docs/app-check/ios/devicecheck-provider)
- [SafetyNet on Android](https://firebase.google.com/docs/app-check/android/safetynet-provider)

App Check currently works with the following Firebase products:

- Realtime Database
- Cloud Storage
- Cloud Functions (callable functions)

The [official Firebase App Check documentation](https://firebase.google.com/docs/app-check) has more information, including about the iOS AppAttest provider, and testing/ CI integration, it is worth a read.

## Installation

Install the plugin by running the following command in the root directory of your project.

```cli
npm install @nativescript/firebase-app-check
```

## iOS requirements

- (iOS) App Check requires you to set the minimum iOS Deployment version to `11.0` or greater in the `build.xconfig` file found in the  `App_Resources/IOS/`.

## Use @nativescript/firebase-app-check

### Activate App Check

To activate App Check, first, call the static `AppCheck.setProviderFactory()` method before calling `firebase().initializeApp()`. If you want to use a custom(an instance of `AppCheckProviderFactory`) App Check provider, pass it to this method to register it.

Next, after calling `firebase().initializeApp()` and it has resolved, call `firebase().appCheck().activate(true)` to initialize App Check.
```ts
import { firebase } from '@nativescript/firebase-core';
import { AppCheck } from '@nativescript/firebase-app-check';

AppCheck.setProviderFactory(); // call before the fb app is initialized 
firebase().initializeApp()
.then(app =>{
    firebase().appCheck().activate(true);
})
```

 The only configuration possible is the token auto refresh. When you call the `activate` method, the provider stays the same but the token auto-refresh setting is changed based on the argument provided.

You must call `activate` before calling any firebase back-end services for App Check to function.


#### Creating a custom App Check provider

The example below shows how to create a custom App Check provider.

```ts
import { firebase } from '@nativescript/firebase-core';
import { AppCheck, AppCheckProviderFactory, AppCheckProvider } from '@nativescript/firebase-app-check';


class AppCheckProviderFactoryImpl extends AppCheckProviderFactory {
	createProvider(app: FirebaseApp) {
        // we could potentiall do something with the app 
        return new AppCheckProviderImpl();
    }
}


class AppCheckProviderImpl extends AppCheckProvider {
    getToken(done){
        // do some call probably http
        // finally call done when you're ready passing in a token along with the expirationDate
        done({
            token: someToken,
            expirationDate: someDate 
        })
    }
}



AppCheck.setProviderFactory(new AppCheckProviderFactoryImpl()); // call before the fb app is initialized 
firebase.initializeApp()
.then(app =>{
    firebase().appCheck().activate(true);
})


```

### Automatic Data Collection

App Check has a `tokenAutoRefreshEnabled` setting, that you can enable with the ``. This may cause App Check to attempt a remote App Check token fetch before user consent. In certain scenarios, like those that exist in GDPR-compliant apps running for the first time, this may be unwanted.

If unset, the `tokenAutoRefreshEnabled` setting will defer to the app's "`automatic data collection`" setting, which is set in the `Info.plist` or `AndroidManifest.xml`

### Using App Check tokens for non-firebase services

The [official documentation](https://firebase.google.com/docs/app-check/web/custom-resource) shows how to use getToken to access the current App Check token and then verify it in external services.

## API
### AppCheck class

### ios
```ts
import { AppCheck } from "@nativescript/firebase-app-check"

appCheck = new AppCheck()
iosAppCheck: FIRAppCheck = appCheck.ios
```
A read-only property that returns the iOS instance of the AppCheck class.

---
### android
```ts
import { AppCheck } from "@nativescript/firebase-app-check"

appCheck = new AppCheck()
androidAppCheck: com.google.firebase.appcheck.FirebaseAppCheck = appCheck.android
```
A read-only property that returns the Android instance of the AppCheck class.

---
### app
```ts
import { AppCheck } from "@nativescript/firebase-app-check"

appCheck = new AppCheck()
app: FirebaseApp = appCheck.app
```
A read-only property that returns an instance of the FirebaseApp class.

---
### constructor()
```ts
import { AppCheck } from "@nativescript/firebase-app-check"

appCheck = new AppCheck(app)
```
Creates an AppCheck object. Optionally, it accepts a FirebaseApp class instance.

| Parameter | Type |
|----------|------- 
| `app`| `FirebaseApp` |_Optional_

---
### setProviderFactory()
```ts
appCheck.setProviderFactory(custom)
```

| Parameter | Type |
|----------|------- 
| `custom`| [AppCheckProviderFactory](#appcheckproviderfactory) |_Optional_: Your custom App Check provider instance.

---
### activate()
```ts
appCheck.activate(isTokenAutoRefreshEnabled)
```
Initializes App Check.

| Parameter | Type 
|----------|------- 
| `isTokenAutoRefreshEnabled` | `boolean`

---
### setTokenAutoRefreshEnabled()
```ts
appCheck.setTokenAutoRefreshEnabled(enabled)
```
| Parameter | Type 
|----------|------- 
| `isTokenAutoRefreshEnabled` | `boolean`


---
### getToken()
```ts
appCheck.getToken(forceRefresh).then((token: AppCheckToken ) =>{

})
```
| Parameter | Type 
|----------|------- 
| `forceRefresh` | `boolean`


---
### AppCheckToken 
#### ios
```ts
iosToken = token.ios
```
#### android
```ts
androidToken = token.android
```
#### token
```ts
tokenStr = token.token
```
#### expireTimeMillis
```ts
expireTime: number = token.expireTimeMillis
```

### AppCheckProviderFactory
```ts
declare abstract class AppCheckProviderFactory {
	abstract createProvider(app: FirebaseApp): AppCheckProvider;
	readonly native;
}
```
## License

Apache License Version 2.0
