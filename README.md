# PinLockView - Make your app more secure

# Overview
PinLockView is library control that enables security into your app by asking a user. If you’ve app that has some sensitive/secure information that you don’t want to leverage you can use this view control.


<img alt="RecyclerView Adapter Library" src="https://github.com/mkrupal09/PinLView/blob/master/main.png" width = "212" height = "375"/>

# Use Case
1. If you’ve any secure screen and you don’t want to redirect without asking user credentials (pin) this is used i.e payment screen
2. If you want to secure entire app like if user leaves your app and comeback to app then without asking credentials(pin) your app won’t open
3. If your app has some screens that only need to be secure then this view controller helps
4. If you only want to ask pin while open app this will works

Download
--------

Grab via Maven:
```xml
<dependency>
  <groupId>com.hb.pinlockview</groupId>
  <artifactId>pinlockview</artifactId>
  <version>1.0</version>
  <type>pom</type>
</dependency>
```
or Gradle:
```groovy
implementation 'com.hb.pinlockview:pinlockview:1.0'
```

# How it works?

## Step 1

Use Pinview to create layout

``` xml
<?xml version="1.0" encoding="utf-8"?>
<layout>

    <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/colorPrimaryDark">


        <com.hb.pinview.PinView
            android:id="@+id/pinView"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:dotDiameter="20dp"
            app:icon="@drawable/ic_instagram"
            app:indicatorType="fixed"
            app:keypadButtonSize="80dp"
            app:keypadShowDeleteButton="true"
            app:keypadTextColor="@color/white"
            app:keypadTextSize="22sp"
            app:pinLength="6" />
    </FrameLayout>
</layout>
```
## Step 2

Create Activity and Find View Refrences 

```java
mPinView = (IndicatorDots) findViewById(R.id.pinView);
```

Intialize pinview for it's required resources

```java
mPinView.initialize(this)
```

Implement the listener interface as follows,

```java
private PinLockListener mPinLockListener = new PinLockListener() {
    @Override
    public void onComplete(String pin) {
        Log.d(TAG, "Pin complete: " + pin);
     }

    @Override
    public void onEmpty() {
        Log.d(TAG, "Pin empty");
    }

    @Override
    public void onPinChange(int pinLength, String intermediatePin) {
         Log.d(TAG, "Pin changed, new length " + pinLength + " with intermediate pin " + intermediatePin);
    }
};
mPinView.setPinLockListener(mPinLockListener);
```
To change length of pin
```java
 mPinView.setPinLength(6)
```

To enable scramble/shuffle layout
```java
mPinView.enableLayoutShuffling()
```

To enable biometric auth use
```java
  mPinview.requestEnableBiometric(object : OnBiometricCallback {
                override fun onBiometricSuccess() {}
                override fun onBiometricFailed() {}
            }) 
            //This will enable biometric view if device has biometric hardware and biometric security added to device

```

To show view animation
```java
        mPinView.startProgress() // To start animation dot indicator view
        mPinView.stopProgress() //Will stop animation for dot indicator view
```



## Step 3  (Optional) Put this code to application class
 
 add dependency
 ```groovy
 implementation "androidx.lifecycle:lifecycle-process:2.2.0"
 ```
 
```java
  class MainApplication : Application() {
    var appComesForeground = false
    // Used to lock app globally (if you want to lock app while coming from background) use this method 
    // place it after application main screen load

    public fun initLocker() {
        ProcessLifecycleOwner.get().lifecycle.addObserver(lockerAppLifecycleObserver)
        registerActivityLifecycleCallbacks(lockerActivityLifecycleCallbacks)
    }

    //This will disable locker will not show pin screen if user comes from background
    public fun disableLocker() {
        ProcessLifecycleOwner.get().lifecycle.removeObserver(lockerAppLifecycleObserver)
        unregisterActivityLifecycleCallbacks(lockerActivityLifecycleCallbacks)
    }

    private val lockerAppLifecycleObserver = object : LifecycleObserver {
        @OnLifecycleEvent(Lifecycle.Event.ON_START)
        fun onStartApp() {
            appComesForeground = true
        }

        @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
        fun onStopApp() {
            appComesForeground = false
        }
    }

    private val lockerActivityLifecycleCallbacks = object : ActivityLifecycleCallbacks {
        override fun onActivityPaused(activity: Activity?) {

        }

        override fun onActivityResumed(activity: Activity?) {
            if (appComesForeground) {
                appComesForeground = false
                activity?.startActivity(PinLockActivity.createIntent(activity))
            }
        }

        override fun onActivityStarted(activity: Activity?) {

        }

        override fun onActivityDestroyed(activity: Activity?) {

        }

        override fun onActivitySaveInstanceState(activity: Activity?, outState: Bundle?) {

        }

        override fun onActivityStopped(activity: Activity?) {

        }

        override fun onActivityCreated(activity: Activity?, savedInstanceState: Bundle?) {

        }
    }
}
```


# Theming

There are several theming options available through XML attributes which you can use to completely change the look-and-feel of this view to match the theme of your app.

# PinView
```xml
  app:pinLength="6"                                       // Change the pin length
  app:keypadTextColor="#E6E6E6"                           // Change the color of the keypad text
  app:keypadTextSize="16dp"                               // Change the text size in the keypad
  app:keypadButtonSize="72dp"                             // Change the size of individual keys/buttons
  app:keypadVerticalSpacing="24dp"                        // Alters the vertical spacing between the keypad buttons
  app:keypadHorizontalSpacing="36dp"                      // Alters the horizontal spacing between the keypad buttons
  app:keypadButtonBackgroundDrawable="@drawable/bg"       // Set a custom background drawable for the buttons
  app:keypadDeleteButtonDrawable="@drawable/ic_back"      // Set a custom drawable for the delete button
  app:keypadDeleteButtonSize="16dp"                       // Change the size of the delete button icon in the keypad
  app:keypadShowDeleteButton="false"                      // Should show the delete button, default is true
  app:keypadDeleteButtonPressedColor="#C8C8C8"            // Change the pressed/focused state color of the delete button
  app:dotEmptyBackground="@drawable/empty"                // Customize the empty state of the dots
  app:dotFilledBackground"@drawable/filled"               // Customize the filled state of the dots
  app:dotDiameter="12dp"                                  // Change the diameter of the dots
  app:dotSpacing="16dp"                                   // Change the spacing between individual dots
  app:indicatorType="fillWithAnimation"                   // Choose between "fixed", "fill" and "fillWithAnimation"
  app:icon="@drawable/icon"                               // Customize the icon which shows at top
```

<h2 id="social">Social Media :fire:</h2>

If you like this library, please tell others about it :two_hearts: :two_hearts:

(https://github.com/mkrupal09/PinLView/blob/master/twitter_icon.png)
(https://github.com/mkrupal09/PinLView/blob/master/facebook_icon.png)

[![Share on Twitter](https://github.com/mkrupal09/PinLView/blob/master/twitter_icon.png)]
[![Share on Facebook](https://github.com/mkrupal09/PinLView/blob/master/facebook_icon.png)]

# License

```
Copyright 2017 aritraroy

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
