---
layout: post
title: "How-To Create a Working Kiosk Mode in Android"
comments: true
language: "EN"
url: "http://www.andreas-schrade.de/2015/02/16/android-tutorial-how-to-create-a-kiosk-mode-in-android/"
permalink: /2015/02/16/android-tutorial-how-to-create-a-kiosk-mode-in-android/

---



## Introduction

This article describes a list of powerful methods to implement a Kiosk Mode in Android. A <strong>Kiosk Mode</strong> is useful if you develop an Android app and want prevent any other applications to run in the foreground. 
<p style="
    border: 3px solid #f12e2e;
    padding: 12px;
          ">
          <a href="https://www.42gears.com/products/surelock/surelock-android/" onclick="trackOutboundLink('https://www.42gears.com/products/surelock/surelock-android/'); return false;" target="_blank"><img style="float:right;margin-left: 16px; margin-right:8px" src="{{ site.url }}/assets/android_kiosk_surelock.png"> </a>
          <b><u>Note:</u></b><br>Not a software developer? <b>Turn any Android tablet or phone into a Kiosk.</b><br>Use <a href="https://www.42gears.com/products/surelock/surelock-android/" onclick="trackOutboundLink('https://www.42gears.com/products/surelock/surelock-android/'); return false;" target="_blank">SureLock</a> to lock them down to only one or a few specific apps.</p>
          
**A few things to consider**

- Exit mechanism: Don't forget to implement *and* test an exit mechanism in your Android Kiosk app.
- Talk to your users: Be careful, if you want to distribute your Kiosk app through the Play Store. Tell your users how they can leave your app before they enter the Kiosk Mode.
- Nothing is completely secure: There are techies like you out there who can bypass your restrictions if the device is not physically secured.

<br>

## Implementation

### Overview

A Kiosk Mode is implemented by disabling various Android features that can be used to leave your app.
The following features are affected:

- The back button
- The home button
- The recent apps button
- The power button
- The volume buttons

### Preparation

First of all we need to make sure your Android Kiosk app starts automatically after booting your device.

Add the following permission as a child of the *manifest* element to your Android manifest:

{% highlight xml %}
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" /> 
{% endhighlight %}

Now, your app has the permission to receive the *RECEIVE\_BOOT\_COMPLETED* broadcast. This message means that the phone was booted.

At next, add an intent filter to the manifest:

{% highlight xml %}
<receiver android:name=".BootReceiver">
    <intent-filter >
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
    </intent-filter>
</receiver>
{% endhighlight %}

Create a class called *BootReceiver* that extends *BroadcastReceiver* and add your code to the *onReceive* method to start your application:

{% highlight java %}
public class BootReceiver extends BroadcastReceiver {

  @Override
  public void onReceive(Context context, Intent intent) {
    Intent myIntent = new Intent(context, MyKioskModeActivity.class);
    myIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    context.startActivity(myIntent);
  }
}
{% endhighlight %}

### Disable the back button

This is a simple task: Just override *onBackPressed* in your Activity class. Please note that you can not override *onBackPressed* inside fragments! 

{% highlight java %}
@Override
public void onBackPressed() {
	// nothing to do here
	// … really      
}
{% endhighlight %}

### Disable the power button

Sadly, disabling the power button is not possible without custom modification to the core of the Android operating system. Nevertheless, it is possible to detect the result of the button press and react on it.

**Short power button press:**

You can detect a short button press by handling the *ACTION\_SCREEN\_OFF* intent and kick the screen back to life with acquiring a wake lock. What a hack!

Please note that you can't declare *ACTION\_SCREEN\_OFF* in the AndroidManifest.xml! You are only allowed to catch them while your application is running. 
For that reason, create a class called *OnScreenOffReceiver* that extends *BroadcastReceiver* and add the following code:

{% highlight java %}
public class OnScreenOffReceiver extends BroadcastReceiver {
  private static final String PREF_KIOSK_MODE = "pref_kiosk_mode";
    
  @Override
  public void onReceive(Context context, Intent intent) {
    if(Intent.ACTION_SCREEN_OFF.equals(intent.getAction())){
      AppContext ctx = (AppContext) context.getApplicationContext();
      // is Kiosk Mode active?
      if(isKioskModeActive(ctx)) {
        wakeUpDevice(ctx);
      }
    }
  }

  private void wakeUpDevice(AppContext context) {
    PowerManager.WakeLock wakeLock = context.getWakeLock(); // get WakeLock reference via AppContext
    if (wakeLock.isHeld()) {
      wakeLock.release(); // release old wake lock
    }

    // create a new wake lock...
    wakeLock.acquire();

    // ... and release again
    wakeLock.release();
  }

  private boolean isKioskModeActive(final Context context) {
    SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(context);
    return sp.getBoolean(PREF_KIOSK_MODE, false);
  }
}
{% endhighlight %}

Create a subclass of *Application* and add the following code:

{% highlight java %}
public class AppContext extends Application {

  private AppContext instance;
  private PowerManager.WakeLock wakeLock;
  private OnScreenOffReceiver onScreenOffReceiver;


  @Override
  public void onCreate() {
    super.onCreate();
    instance = this;
    registerKioskModeScreenOffReceiver();
  }

  private void registerKioskModeScreenOffReceiver() {
    // register screen off receiver
    final IntentFilter filter = new IntentFilter(Intent.ACTION_SCREEN_OFF);
    onScreenOffReceiver = new OnScreenOffReceiver();
    registerReceiver(onScreenOffReceiver, filter);
  }

  public PowerManager.WakeLock getWakeLock() {
    if(wakeLock == null) {
      // lazy loading: first call, create wakeLock via PowerManager.
      PowerManager pm = (PowerManager) getSystemService(Context.POWER_SERVICE);
      wakeLock = pm.newWakeLock(PowerManager.FULL_WAKE_LOCK | PowerManager.ACQUIRE_CAUSES_WAKEUP, "wakeup");
    }
    return wakeLock;
  }
}
{% endhighlight %}


Add the permission *WAKE_LOCK* to your manifest:

{% highlight xml %}
<uses-permission android:name="android.permission.WAKE_LOCK" />
{% endhighlight %}

Register your subclass of *Application* in your manifest (android:name=".AppContext"):

{% highlight xml %}
<application
        android:name=".AppContext"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name">
{% endhighlight %}

To make the wake up a bit smarter, add the following line in your activity (before *setContentView* is called!). This line deactivates the lock screen:

{% highlight java %}
getWindow().addFlags(WindowManager.LayoutParams.FLAG_DISMISS_KEYGUARD);
{% endhighlight %}

For example:
{% highlight java %}
@Override
protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  getWindow().addFlags(WindowManager.LayoutParams.FLAG_DISMISS_KEYGUARD);
  setContentView(R.layout.my_activity);
  // … further layout init
}
{% endhighlight %}


**Long power button press:**

Now we come to my favorite hack: It is very simple but powerful.

Add the following snippet to your activity. It will surely prevent long press button.

{% highlight java %}
@Override
public void onWindowFocusChanged(boolean hasFocus) {
  super.onWindowFocusChanged(hasFocus);
  if(!hasFocus) {
	  // Close every kind of system dialog
    Intent closeDialog = new Intent(Intent.ACTION_CLOSE_SYSTEM_DIALOGS);
    sendBroadcast(closeDialog);
  }
}
{% endhighlight %}

The idea is simple: in case any system dialog pops up, we kill it instantly by firing an *ACTION\_CLOSE\_SYSTEM\_DIALOG* broadcast.


### Disable the volume button

If necessary, you can easily deactivate the volume buttons by consuming both button calls. Just override *dispatchKeyEvent* in your Activity and handle the volume buttons:

{% highlight java %}
private final List blockedKeys = new ArrayList(Arrays.asList(KeyEvent.KEYCODE_VOLUME_DOWN, KeyEvent.KEYCODE_VOLUME_UP));

@Override
public boolean dispatchKeyEvent(KeyEvent event) {
  if (blockedKeys.contains(event.getKeyCode())) {
    return true;
  } else {
    return super.dispatchKeyEvent(event);
  }
}
{% endhighlight %}


### Disable the home button and detect when new applications are opened

Since Android 4 there is no effective method to deactivate the home button. That is the reason why we need another little hack. In general the idea is to detect when a new application is in foreground and restart your activity immediately.  

At first create a class called *KioskService* that extends *Service* and add the following snippet:

{% highlight java %}
public class KioskService extends Service {

  private static final long INTERVAL = TimeUnit.SECONDS.toMillis(2); // periodic interval to check in seconds -> 2 seconds
  private static final String TAG = KioskService.class.getSimpleName();
  private static final String PREF_KIOSK_MODE = "pref_kiosk_mode";

  private Thread t = null;
  private Context ctx = null;
  private boolean running = false;

  @Override
  public void onDestroy() {
    Log.i(TAG, "Stopping service 'KioskService'");
    running =false;
    super.onDestroy();
  }

  @Override
  public int onStartCommand(Intent intent, int flags, int startId) {
    Log.i(TAG, "Starting service 'KioskService'");
    running = true;
    ctx = this;
    
    // start a thread that periodically checks if your app is in the foreground
    t = new Thread(new Runnable() {
      @Override
      public void run() {
        do {
          handleKioskMode();
          try {
            Thread.sleep(INTERVAL);
          } catch (InterruptedException e) {
            Log.i(TAG, "Thread interrupted: 'KioskService'");
          }
        }while(running);
        stopSelf();
      }
    });

    t.start();
    return Service.START_NOT_STICKY;
  }

  private void handleKioskMode() {
    // is Kiosk Mode active? 
	  if(isKioskModeActive()) {
	    // is App in background?
      if(isInBackground()) {
        restoreApp(); // restore!
      }
    }
  }

  private boolean isInBackground() {
    ActivityManager am = (ActivityManager) ctx.getSystemService(Context.ACTIVITY_SERVICE);
	
    List<ActivityManager.RunningTaskInfo> taskInfo = am.getRunningTasks(1);
    ComponentName componentInfo = taskInfo.get(0).topActivity;
    return (!ctx.getApplicationContext().getPackageName().equals(componentInfo.getPackageName()));
  }

  private void restoreApp() {
    // Restart activity
    Intent i = new Intent(ctx, MyActivity.class);
    i.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    ctx.startActivity(i);
  }
  
  public boolean isKioskModeActive(final Context context) {
    SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(context);
    return sp.getBoolean(PREF_KIOSK_MODE, false);
  }

  @Override
  public IBinder onBind(Intent intent) {
    return null;
  }
}
{% endhighlight %}

Add the following method in your *AppContext* class to start the service via application context creation.

{% highlight java %}
@Override
public void onCreate() {
  super.onCreate();
  instance = this;
  registerKioskModeScreenOffReceiver();
  startKioskService();  // add this
}

private void startKioskService() { // ... and this method
  startService(new Intent(this, KioskService.class));
}
{% endhighlight %}

Last, add the service declaration and the permission for retrieving the foreground process to the manifest:

{% highlight xml %}
<service android:name=".KioskService" android:exported="false"/>

<uses-permission android:name="android.permission.GET_TASKS"/>
{% endhighlight %}

Basically, the thread checks every two seconds if your application is running in foreground. If not, the thread will immediately recreate your activity and your Android Kiosk is visible again.


### Prevent screen dimming

It is also very easy to keep the screen bright as long as your app is visible (also forever). You only have to add the following flag to your root layout:

{% highlight xml %}
android:keepScreenOn="true"
{% endhighlight %}

For example:
{% highlight xml %}
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:id="@+id/myActivityRootLayout"
  android:layout_width="match_parent" 
  android:layout_height="match_parent"
  android:keepScreenOn="true">
  
  <!-- your layout -->
  
</RelativeLayout>
{% endhighlight %}

That's it!<br>

## Conclusion

Developing a kiosk-based application is not the easiest part in Android development, but it is definitely possible to create a "robust" Kiosk Mode. The only disadvantage is the long list of (very) dirty hacks.
If you do not have very specific requirements, it is a good choice to use an already existing Kiosk app. 

<br>
<a href="https://github.com/andreasschrade/android-kiosk-mode" target="_blank">Download example project</a>                    <br>
<br>

<strong>Update February 2017:</strong>

### Prevent status bar expansion / disable status bar pull down

There is no option that disables the status bar expansion. However, it is possible to add an invisible layer on top of the status bar that catches every click/touch. That is just another workaround to make a real Android Kiosk happen:

{% highlight java %}
public static void preventStatusBarExpansion(Context context) {
    WindowManager manager = ((WindowManager) context.getApplicationContext().getSystemService(Context.WINDOW_SERVICE));

    WindowManager.LayoutParams localLayoutParams = new WindowManager.LayoutParams();
    localLayoutParams.type = WindowManager.LayoutParams.TYPE_SYSTEM_ERROR;
    localLayoutParams.gravity = Gravity.TOP;
    localLayoutParams.flags = WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE|WindowManager.LayoutParams.FLAG_NOT_TOUCH_MODAL|WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN;

    localLayoutParams.width = WindowManager.LayoutParams.MATCH_PARENT;

    int resId = context.getResources().getIdentifier("status_bar_height", "dimen", "android");
    int result = 0;
    if (resId > 0) {
      result = context.getResources().getDimensionPixelSize(resId);
    } else {
      // Use Fallback size:
      result = 60; // 60px Fallback
    }

    localLayoutParams.height = result;
    localLayoutParams.format = PixelFormat.TRANSPARENT;

    customViewGroup view = new CustomViewGroup(context);
    manager.addView(view, localLayoutParams);
}

public static class CustomViewGroup extends ViewGroup {
    public CustomViewGroup(Context context) {
        super(context);
    }

    @Override
    protected void onLayout(boolean changed, int l, int t, int r, int b) {
    }

    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        // Intercepted touch!
        return true;
    }
}
{% endhighlight %}

Necessary permission:

{% highlight xml %}
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/> 
{% endhighlight %}


<script>
var trackOutboundLink = function(url) {
   ga('send', 'event', 'outbound', 'click', url, {
     'transport': 'beacon',
     'hitCallback': function(){document.location = url;}
   });
}
</script>