---
layout: post
title: "Android Interview Questions"
comments: true
language: "EN"

---

## Android Core

### What is Android?

Android is an open-sourced mobile operating system developed by Google and based on the Linux kernel. Android is designed primarily for touchscreen devices (smartphones, tablets).

### What is the APK format?

Android application package (APK) is the package file format used to distribute and install application software onto Android.

### What is Dalvik?

Dalvik is a register-based Virtual Machine (VM). Every Android app runs in its own process with its own instance of the Dalvik VM.

Note: Dalvik was entirely replaced by ART in Android 5.0 Lollipop.

### What is ART?
ART (Android Runtime) is the new runtime environment for Android apps. ART improves the execution efficiency by using AOT (Ahead-of-Time) compilation.

### What is the "UI thread"?

An Android app runs in its own process and can use multiple threads. The thread that the app will be executed upon, most of the time, is called the "main thread" or the "UI thread".

### What is Instant Run?

Instant Run has the intention to increase the development speed of Android apps. Instead of rebuilding the whole app, Android is trying to patch the existing app on the Android device to reflect your changes. 

### What is an Android manifest file?

Every Android app must have an AndroidManifest.xml file in its root directory. The AndroidManifest.xml file provides essential information about the application to the Android system, which the system must have before it can run any of the application's code.

<u>Background:</u> It contains information about:

- the Java package of the application
- app components like activities, services, broadcast receivers and content providers
- necessary permissions

### What is ADB?

Android Debug Bridge (adb) is a command-line tool that lets you communicate with an Android device. It provides a variety of device actions, such as installing and debugging apps.

### What is an Intent?
An Intent is basically a message that is passed between components like Activities or Services. It acts as a trigger to do something.

<u>Background:</u> Intents are asynchronous and allow you to interact with components from the same application as well as with components from other applications.
The primary pieces of information in an Intent are:

- Action: The generic action to perform (ACTION\_VIEW -> view, ACTION\_EDIT -> edit, ...)
- Extras: The data to operate on stored in a key-value mapping (Bundle)
- Component name: The name of the component to start. This value makes an Intent explicit (e.g. com.example.AnotherActivity.class)
- Flags: Optional metadata for the Intent

### What is an Implicit Intent?

An implicit Intent does not name a specific component. It declares a general action to perform.
The action specifies the thing that the app wants to do.

<u>Background:</u> The system is looking for an appropriate component to start:

- If multiple components are compatible with the action, the system shows a dialog so the user can pick which app to use
- If there is no appropriate component available on the device that can handle the action, your app will crash immediately!

<u>Example:</u> If an app wants to trigger a phone call, it only has to specify the corresponding action (ACTION\_DIAL):

{% highlight java %}
Uri number = Uri.parse("tel:4213371337");
Intent callIntent = new Intent(Intent.ACTION_DIAL, number);
if (callIntent.resolveActivity(getPackageManager()) != null) {
	// the system can resolve the Intent
}
{% endhighlight %}

### What is an Explicit Intent?

An explicit Intent specifies the component by the fully-qualified class name to start. This is the common case to start a component in the own app.

<u>Example:</u>
{% highlight java %}
Intent startIntent = new Intent(myContext, AnotherActivity.class);
{% endhighlight %}

### What is a Sticky Intent?

A sticky Intent is a Intent that has been sent as a sticky Broadcast, meaning the Intent stays around after the broadcast is complete.

<u>Example:</u> The Android system uses sticky broadcasts to notify receivers that the battery level has been changed (ACTION\_BATTERY\_CHANGED).

When you call registerReceiver() for that action you will always get the latest Intent for that action. You do not have to wait for the next broadcast!

<u>Note:</u> Sticky broadcasts are deprecated in Android 5+.

### What is a PendingIntent?

A PendingIntent wraps a regular Intent that specifies an action to take in the future.
At the same time it acts as a token for foreign app components (e.g. AlarmManager, NotificationManager, AppWidgetManager). This token gives the foreign application the permission to execute the app internal Intent when a condition is met (e.g. AlarmManager triggers a PendingIntent at a specific time) 

### What are common use cases for using an Intent?

- starting an internal Activity (explicit Intent)
- starting an internal Service (explicit Intent)
- starting an external Activity/Application (implicit Intent)
- delivering a broadcast

### What is a Service?

A service is an application component without user interface that can perform long running operations in the background (= the corresponding app does not have to be in the foreground).

<b>Important:</b> A Service runs in the main thread and does not create its own thread. It is important to create a new thread inside the Service instance for CPU-intensive or blocking operations.

<u>Background:</u> There exist two important types of services:

1. <b>Started Service:</b> A started service (usually started via startService()) runs in the background indefinitely. After the service finished its work (e.g. downloading a file), it is necessary to stop the service by calling stopSelf() or stopService().

Lifecycle calls: onCreate() -> onStartCommand() -> RUNNING -> onDestroy()

2. <b>Bound Service:</b> A bound service offers a client-server interface to an application component (e.g. Activity). This type of service runs only as long as another app component is bound to it.

Lifecycle calls: onCreate() -> onBind() -> RUNNING -> onUnbind() -> onDestroy()

A mixture of both types does also work!

### What is an IntentService?

The IntentService is a subclass of Service that uses a worker thread to handle all of the start requests. All tasks are executed sequentially on a separate worker thread. The IntentService cannot run tasks in parallel. 

<u>Background:</u> There is no need to spawn an extra thread and there is also no need to call stopSelf(). The IntentService stops itself automatically as soon as all tasks/start requests have been handled.

## When to use IntentService?
An IntentService is typically used for long running "fire and forget tasks".

## What is AsyncTask?
AsyncTask is an abstract class that allows to run short operations in the background and publish results easily on the UI thread. 

## When to use AsyncTask?
AsyncTask is a convenient way to perform short operations (a few seconds) from within an Activity or Fragment.

When using an Asynctask inside an Activity or Fragment, check if a running AsyncTask is canceled when the user leaves the Activity/Fragment.
Implement AsyncTask inside an Activity or Fragment always as a static inner class and avoid references to the outer Activity/Fragment to avoid memory leaks.

## What is the JobScheduler API?

JobScheduler is an abstract class that allows developers to create jobs that execute in the background when certain conditions are met. Typical conditions are:

 - device is plugged into a power source
 - device is connected to Wifi

<b>Important:</b> The job runs on the main thread. It is necessary to use another thread for longer tasks.

## What is a Handler typically used for?
Typically it is used to perform an action on a different thread than your own.

It is also used to schedule messages and runnables to be executed at some point in the future.

<u>Background:</u> In the most cases, you'll use a Handler in a background thread to perform some kind of action in the main thread. The Handler objects registers itself in the thread in which it is created and provides a communication channel to this thread.

Example:
{% highlight java %}
public class MyActivity extends Activity {
	private ProgressBar progress;

	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.example);
		progress = (ProgressBar) findViewById(R.id.myProgressBar);
	}

	public void startSuperLongProcessing(View view) {
		Runnable runnable = new Runnable() {
			@Override
			public void run() {
				progress.post(new Runnable() { // uses the Handler from the View element
					@Override
					public void run() {
						progress.setProgress(100); // executes this statement on the Main thread
					}
				});
			}
		};
		new Thread(runnable).start(); // starts the thread
	}
}
{% endhighlight %}

### What is a ContentProvider?
A Content Provider is part of an Android application that manages access to a repository of data.

### What is a ContentProvider typically used for?
It is typically used to provide a way to share data with other apps.

### When should you implement a ContentProvider?
You should consider the usage of a ContentProvider if you plan to share data with other apps.

### What is SharedPreference?

SharedPreference is a simples mechanism to store data in Android. Data is stored in a collection of key-values in a file.

### What is an ANR message?
Anr is the acronym for "Application Not Responding." This is a dialog that the system displays if an app cannot respond to user input.

### How to avoid ANR messages?
By using a worker thread for blocking I/O operations or other long running operations.

<u>Background:</u> Android apps normally run entirely on a single thread (UI thread).
If an app executes a long running operation on that thread and cannot response to an user input event (e.g. screen touch event) within 5 seconds, the system will show an ANR dialog.

## Android UI

### What is an Activity?
An Activity represents the presentation layer in Android. It provides a screen with which a user can interact in order to do something.

<u>Background:</u> An Android application usually consists of multiple Activities that are loosely bound to each other and can be switched in between during runtime of the application.

### What are the most important lifecycle methods of an Activity?
onCreate(Bundle), onStart(), onResume(), onPause(), onStop(), onDestroy()

### What livecycle methods are part of the visible lifecycle?
onStart(), onResume(), onPause(), onStop()

<u>Background:</u> During this time the user can see the Activity. However, the Activity may not be in the foreground and interacting with the user.

### What lifecycle methods are part of the foreground lifecycle?
onResume(), onPause()

<u>Background:</u> The Activity is in front of all others Activities during this time. The user can interacting with the Activity. 

### What are the four essential states of an Activity?

- Active: if the Activity is active (it can receive user input) and visible
- Paused: if the Activity is visible but not active
- Stopped: if the Activity is not visible
- Destroyed: when the activity process is killed

### When does the system directly call "onDestroy()" after it called "onCreate(Bundle)"? (without calling onStart(), onResume(), onStop(), onPause())
By calling finish() within the onCreate(Bundle) method, the system does not call any further lifecycle methods except onDestroy().

### Activity A start Activity B. Which lifecycle methods are called and in what order?

1. Activity A's onPause() method is called.
2. Activity B's onCreate(), onStart(), and onResume() methods are called in sequence. Activity B is in foreground now.
3. Activity A's onStop() method is called. Activity A is no longer visible on screen.

<b>Important:</b> Please notice the overlap in the Activity transition. Activity A is <b>not</b> completely stopped before Activity B is created.

### Can you describe two scenarios in which your Activity gets destroyed due to normal app behaviour?

1. When the user presses the Back button
2. Calling the Activity.finish() method

<u>Background:</u> In both cases the Activity instance is gone forever. The Activity is no longer needed.

Also a configuration change during runtime (such as screen orientation, keyboard availability, language,...) triggers an Activity re-creation: The current instance is destroyed (onDestroy() is called) and a new instance is created (onCreate() is called). It is important to store the Activity state during this re-creation process (via Bundle).

### Can you describe a scenario in which your Activity gets destroyed due to a system behaviour?
The Android system may destroy the process(!) containing your Activity to recover memory.

<u>Background:</u> This happens if the Activity is in the Stopped state and hasn't been used in a long time, or if the current foreground Activity requires more memory.
The system is storing the <i>instance state</i> in a Bundle object. The saved state is used to restore the previous state.

<b>Important:</b> Android is killing the whole process and not only the Activity instance. 

### How does the system store the Activity state?
The system calls the onSaveInstance() method and stores the instance state in a collection of key-value pairs.

<b>Important:</b> You always have to call the superclass implementation of onSaveInstanceState(). The default implementation saves the state of the view hierarchy. This requires that each view has an unique ID (android:id).

<u>Example:</u>
{% highlight java %}
@Override
public void onSaveInstanceState(Bundle savedInstanceState) {
    savedInstanceState.putInt(MY_VAR_KEY, myVarValue);

    // Always call the superclass method
    super.onSaveInstanceState(savedInstanceState);
}
{% endhighlight %}

### How can an Activity recover its previous state?
Either by using the <i>Bundle</i> instance that the system passes to onCreate(Bundle) or by implementing the onRestoreInstanceState(Bundle) method.

<u>Example:</u>

{% highlight java %}
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // Always do a null check first!
    if (savedInstanceState != null) {
        // Restore value from instance state
        myVarValue = savedInstanceState.getInt(MY_VAR_KEY);
    } else {
        // First initialization - Nothing to restore!
    }
}
{% endhighlight %}

Otherwise use onRestoreInstanceState(Bundle):

{% highlight java %}
@Override
public void onRestoreInstanceState(Bundle savedInstanceState) {
    // Always call the superclass method
    super.onRestoreInstanceState(savedInstanceState);

    // Restore values from instance state
    myVarValue = savedInstanceState.getInt(MY_VAR_KEY);
}
{% endhighlight %}

### What is a Fragment?
A fragment is a modular section of an Activity, which has its own lifecycle. It can be added or removed while an Activity is running and can also be reused in different activities.

<u>Background:</u> To create a Fragment you have to subclass the Fragment class. You have to provide a public no-argument constructor, because Android will often re-instantiate a Fragment class when needed (-> state restore).

### What is the main purpose of a Fragment?
The main purpose of a Fragment is to support a more dynamic UI (tablets, smartphones) and also to make the reuse of UI components a lot easier.

A Fragment can also exist without its own UI as an invisible worker for the Activity.

A Fragment is closely tied to the Activity it is in. When the Activity is paused, so are all fragments in it; When the Activity is destroyed, so are all fragments in it.

### What are two ways to add a Fragment to an Activity?

A Fragment can be declared inside the Activity's layout file.

{% highlight xml %}
@Override
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment android:name="com.example.MyFragment"
            android:id="@+id/my_fragment"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
</FrameLayout>
{% endhighlight %}

<u>Background:</u>

The Android system inserts the View object returned by the Fragment's onCreateView method directly in place of the &lt;fragment&gt; element.


Otherwise, a fragment can programmatically added at runtime.
You can add, remove or replace a Fragment during runtime by using the APIs from FragmentTransaction.

{% highlight java %}
FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

ExampleFragment newFragment = new ExampleFragment();
fragmentTransaction.replace(R.id.my_fragment_container, newFragment);
fragmentTransaction.addToBackStack(null);

fragmentTransaction.commit();
{% endhighlight %}


"my\_fragment\_container" specifies a ViewGroup resource in which to place the Fragment.

### You are replacing a Fragment with another. How can you ensure that the user can return to the previous fragment by pressing the <i>Back</i> button?

1. You have to add the call addToBackStack() inside the FragmentTransaction.

{% highlight java %}
FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

ExampleFragment fragment = new ExampleFragment();
fragmentTransaction.replace(R.id.my_fragment_container, fragment);
fragmentTransaction.commit();
{% endhighlight %}

2. You have to override onBackPressed() in the main Activity class:

{% highlight java %}
@Override
public void onBackPressed() {
    if (getFragmentManager().getBackStackEntryCount() > 0) {
        getFragmentManager().popBackStack(); // reverses the last transaction
    } else {
        super.onBackPressed(); // returns to the previous Activity
    }
}
{% endhighlight %}

<b>Important:</b> If you do not call addToBackStack() inside the FragmentTransaction that removes a Fragment, then that Fragment is destroyed when the transaction is committed and the user cannot navigate back to it.

However, if you call addToBackStack() when removing a Fragment, then the Fragment is stopped and will be resumed if the user goes back.
The "removed" Fragment remains in <i>created state</i> and only its view is destroyed.

### What is the difference between a Fragment and an Activity?
An Activity represents a full UI screen that acts standalone. An Activity can exist without any Fragment in it.
A Fragment is always <b>a part</b> of an Activity and can't exist independently. An Activity can have one or more fragments.

### How does the system store the Fragment state?
The system calls the onSaveInstanceState() method and stores the instance state in a collection of key-value pairs.

<b>Important:</b> You always have to call the superclass implementation of onSaveInstanceState(). The default implementation saves the state of the view hierarchy. This requires that each view has an unique ID (android:id).

### How can a Fragment recover its previous state?
By restoring the instance state either in onCreate(), onCreateView() or onActivityCreated().

### What does the Fragment's method setRetainInstance(boolean)?

1. setRetainInstance(true): The Fragment's state will be retained (and not destroyed!) across configuration changes (e.g. screen rotate). The state will be retained even if the configuration change causes the "parent" Activity to be destroyed. However, the view of the Fragment gets destroyed!

	Lifecycle Calls:<br>
	onPause() -> onStop() -> onDestroyView() -> onDetach()<br>
	onAttach() -> onCreateView() -> onStart() -> onResume()

2. setRetainInstance(false): The Fragment's state will not be retained across configuration changes (default).

	Lifecycle Calls:<br>
	onPause() -> onStop() -> onDestroyView() -> <b>onDestroy()</b> -> onDetach()<br>
	onAttach() -> <b>onCreate()</b> -> onCreateView() -> onStart() -> onResume()

<b>Important:</b> setRetainInstance(true) does not work with fragments on the back stack. setRetainInstance(true) is especially useful for long running operations inside Fragments which do not care about configuration changes.

### What does a ViewPager?
A ViewPager is a layout manager that allows users to flip left and right through pages (typically Fragments) of data.

<u>Background:</u> FragmentPagerAdapter and FragmentStatePagerAdapter are two subclasses of ViewPager:

- FragmentPagerAdapter:
	- good for a fixed or small number of pages (Fragments)
	- it never removes a Fragment instance once it's created. It only detaches the View from the Fragment (-> onDestroyView())
- FragmentStatePagerAdapter:
	- good for large or unknown number of pages (Fragments)
	- it completely removes Fragment instances once they are out of reach (configurable value)

### What is a task?

A task is a stack (LiFo: “Last in, First out”) which contains a collection of activity instances (also known as back stack). The system can hold multiple tasks at the same time but only one task is in the foreground.  

<u>Example:</u> If the user starts an application which has not been used recently, then a new task is created and the main activity (e.g. Activity A) for that application opens. When Activity A starts Activity B, Activity A is stopped (Android retains the state of Activity A). If the user presses the Back button, Activity A resumes and the current Activity B gets destroyed (Android does not retain the state of Android B).

### What are “launch modes”?

A launch mode defines how a new instance or the existing instance of an activity is associated with the current task.

The launch mode of an Activity supports four different modes:

1. standard (default): Multiple instances of the activity class can be instantiated and multiple instances can be added to the same task or different tasks. This is the common mode for most of the activities.

2. singleTop: The difference from "standard" is, if an instance of the Activity already exists at the top of the current task and the system routes the intent to this Activity, no new instance will be created because it will fire off an onNewIntent() method instead of creating a new Activity instance.

3. singleTask: A new task will <b>always</b> be created and a new Activity instance will be pushed to the task as the root one. However, if any activity instance exists in any tasks, the system routes the intent to that activity instance through the onNewIntent() method call. In this mode, activity instances can be pushed to the same task. Result: A certain Activity can not exist in multiple tasks at the same time.

4. singleInstance: Same as singleTask, except that the no activities instance can be pushed into the same task of the singleInstance’s. 

### Why isn't it a good idea to start a thread when a BroadcastReceiver receives an Intent in its onReceive() method? How can you solve this issue?

As soon as the execution of onReceive() is finished, the system may kill the process at any time (and that will also kill the spawned thread)

Solution: A good way is to use a JobService instead of a thread. By doing so, the system knows that the process is still needed.


### What is the difference between Serializable and Parcelable?

- Serializable is a Java marker interface. A class that implements this interface allows the serialization of an object from this type in certain situations.

- Parcelable is an Android specific interface where you have to implement the serialization by yourself. The parcelable process is much faster than Serializable (= no need for reflection). Parcelable is always the default choice in Android.

<b>Background:</b> Serialization is the process of converting an object instance into a series of bytes, so that the object can be easily saved to persistent storage or transmited across a communication channel (e.g. network).

## Android Design and XML

### What is the difference between Relativelayout and LinearLayout

LinearLayout: Arranges view elements either vertically or horizontally.

RelativeLayout: Arranges view elements relative to parent or to other view elements.

### What is the difference and similarities of ListViews and GridViews?

Both layout components displays a list of scrollable items. The items are inserted by using an Adapter.

- GridView is a ViewGroup that displays items in a two-dimensional, scrollable grid.
- ListView is a ViewGroup that displays a vertical list of scrollable items. 

### What is the GridLayout?
GridLayout is a ViewGroup that sets things up in a grid with rows and columns.
It is like a TabletLayout but does also support columnSpan and rowSpan.

### Which image files are typically used in Android?
PNG is the overall preferred format in Android.

### What is a nine-patch image?
A nine-patch image is a scalable image resource that has 9 areas, called patches, that scale separately.

### How does Android recognize a nine-patch image?
Android handles every image file as nine-patch image that has the "file extension" 9.png

### What is the difference between View.GONE and View.INVISBLE?

- View.INVISIBLE: The view is invisible, but it still takes up space for layout purposes (the view is hidden).
- View.GONE: The view is invisible, and it doesn't take any space for layout purposes (the view is removed).

### What are typical subdirectories that the "res" directory does contain?

The "res" folder contains various resource files:

- res/drawable/* -> images and nine-patch files.
- res/layout/ -> XML-based UI layout files.
- res/values/ -> strings, colors, dimensions, ...
- res/menu/ -> menu specification files
- res/raw/ -> raw files like a CSV file, movie clip or audio clip (mp3)
- res/xml/ -> general XML files

## What is the difference between wrap\_content and match\_parent?

- wrap\_content: the widget should take up as much room as its contents require
- match\_parent: the widget should fill up all remaining available space in its enclosing container

Background: "fill\_parent" is an older synonym for "match\_parent". It is recommended to use match\_parent instead of fill\_parent.

### What is the difference between px, dp and sp?

- px (Pixel): corresponds to actual pixels on the screen
- dp (Density-independent Pixel): abstract unit that is based on the physical density of the screen. One dp is one pixel on a 160 dpi screen (the unit is relative to a 160 dpi screen), but one dp equals two hardware pixels on a 320 dpi screen
- sp (Scale-independent Pixel): same as dp but it is also scaled by the user's font size preference. This unit is used to specify font sizes.

### What is an Adapter
An Adapter is a bridge between the model data and that data’s visual representation in the AdapterView.

<u>Background:</u> Use the BaseAdapter class as the foundation to build your own Adapter implementation. 

### What is the ViewHolder-Pattern?

The ViewHolder design pattern can be used to increase the speed at which a ListView renders data.

The pattern avoids the repeated lookup of view resources. The number of times which the findViewById() method is invoked is drastically reduced, existing views do not have to be garbage collected and new views do not have to be inflated. The view references for every row are stored in a simple object for later reuse.

### What is the benefit of an Android library project?
An Android library project allows the distribution of resources (e.g. layouts, themes) and manifest entries as well as Java Code (e.g. activites).
It makes the process of creating multiple apps from the same code base easier.

<u>Background:</u> The typical library project is distributed as AAR (Android archive file) file.

### What is the Android Support Library?

The Android Support Library is a library distributed by Google that contains backports as well as new widgets and classes.

The overall idea is to make as much as possible of the Android API available for as much as possible devices.

Background:
Originally a single library, the Android Support Library evoled into a suite of libraries:

- support-v13: It contains backports working back to API Level 13 or newer.
- appcompat-v7: Backport of the action bar working back to API Level 7
- recyclerview-v7: Provides the RecyclerView widget.

<b>Important:</b> Starting with Support Library version 24.2.0, the minimum supported API level has changed to API level 9 for all support library packages. The v# package notation does not longer indiciate the real minimum API support level. Example: support-v7 library requires a minimum API level of 9 and not 7.

The SDK Manager provides access to the support libraries (extras/Android Support).

The compileSdkVersion should be the same as the major version of the used Android Support library.

## What is the intention of StrictMode in Android?
StrictMode helps to determine if certain unpleasant things happen inside your app during development phase. For example, it can warn (via logcat, dialog or crash) if you are doing too much things on the main application thread that might cause a janky user experience.

<b>Important:</b> Never use StrictMode in production code!

## What happens with your app if the device goes into "Doze Mode"?
Scheduled alarms (via AlarmManager), jobs (via JobScheduler), and syncs (via SyncManager) will be ignored by default, except during occasional "idle maintenance windows".

## Android Testing

### What is the difference between unit testing and instrumentation testing?

1. Unit testing:
	- All unit tests bypass the Android system and run straight on the developer machine.
	- Unit tests cannot use much of the Android SDK
	- Unit tests run quickly
	- Unit tests are placed inside the "test" folder

2. Instrumentation testing:
	- Test code and production code combined is running in a single process in a single copy of the VM (Dalvik or ART)
	- Instrumentation tests are placed inside the "androidTest" folder
