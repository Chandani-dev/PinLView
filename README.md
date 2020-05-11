# PinLockView make your app more secure (v 1.0) 

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

``` xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:tools="http://schemas.android.com/tools"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/colorPrimaryDark">

        <androidx.appcompat.widget.AppCompatImageView
            android:id="@+id/ivAppIcon"
            android:layout_width="56dp"
            android:layout_height="56dp"
            android:layout_centerHorizontal="true"
            android:layout_marginTop="50dp"
            android:src="@drawable/ic_instagram"
            app:layout_constraintBottom_toTopOf="@id/tvTitle"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

        <androidx.appcompat.widget.AppCompatTextView
            android:id="@+id/tvTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerHorizontal="true"
            app:fontFamily="sans-serif-condensed-medium"
            android:layout_marginTop="30dp"
            android:fontFamily="sans-serif-thin"
            android:gravity="center"
            android:maxLines="1"
            android:text="Enter your pin"
            android:textColor="@color/white"
            android:textSize="18sp"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toBottomOf="@id/ivAppIcon" />

        <com.hb.pinlockview.IndicatorDots
            android:id="@+id/indicator_dots"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            android:layout_marginTop="20dp"
            app:dotDiameter="20dp"
            app:indicatorType="fixed"
            app:layout_constraintTop_toBottomOf="@+id/tvTitle" />

        <com.hb.pinlockview.BiometricView
            android:layout_marginTop="30dp"
            android:id="@+id/biometric_view"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toBottomOf="@id/indicator_dots"
            app:layout_constraintBottom_toTopOf="@id/pin_lock_view"
            android:layout_width="50dp"
            android:layout_height="50dp"/>

        <com.hb.pinlockview.PinLockView
            android:id="@+id/pin_lock_view"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/indicator_dots"
            android:layout_centerHorizontal="true"
            android:layout_marginTop="20dp"
            app:keypadButtonSize="80dp"
            app:keypadShowDeleteButton="true"
            app:keypadTextColor="@color/white"
            app:keypadTextSize="22sp"
            app:layout_constraintBottom_toBottomOf="parent"
            android:layout_marginBottom="20dp"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent" />
    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```
## Step 2

Find View Refrences and attach indicatordots to pinlock view **MUST**

```java
mIndicatorDots = (IndicatorDots) findViewById(R.id.indicator_dots);
mPinLockView = (PinLockView) findViewById(R.id.pin_lock_view);
biometricView = (BiometricView) findViewById(R.id.biometric_view);
mPinLockView.attachIndicatorDots(mIndicatorDots); 

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
mPinLockView.setPinLockListener(mPinLockListener);
```
To enable biometric auth use
```java
biometricView.requestEnableBiometric() //This will enable biometric view if device has biometric hardware and biometric security added to device
 biometricView.setOnClickListener {
            biometricView.promptBiometric(this,
                    object : BiometricView.OnBiometricCallback {
                        override fun onBiometricFailed() {

                        }

                        override fun onBiometricSuccess() {
                            onAuthenticationSuccess() 
                        }
                    })
        }
```

To show view animation
```java
        binding.indicatorDots.startProgress() // To start animation dot indicator view
        binding.pinLockView.stopInputs() //Will stop taking inputs
        indicatorDots.stopProgress() //Will stop animation for dot indicator view
        pinLockView.startInputs() //Will Start taking inputs
        indicatorDots.showErrorAnimation() //Will show error animation for dot indicator ( vibrate animation with vibrate)
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

# PinLockView
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
  
```

# IndicatorDots
```xml
  app:dotEmptyBackground="@drawable/empty"                // Customize the empty state of the dots
  app:dotFilledBackground"@drawable/filled"               // Customize the filled state of the dots
  app:dotDiameter="12dp"                                  // Change the diameter of the dots
  app:dotSpacing="16dp"                                   // Change the spacing between individual dots
  app:indicatorType="fillWithAnimation"                   // Choose between "fixed", "fill" and "fillWithAnimation"
```
