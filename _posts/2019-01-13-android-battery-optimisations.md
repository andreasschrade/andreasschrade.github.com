---
layout: post
title: "Battery saving features in Android"
comments: true
language: "EN"


---

This article is all about power-saving features in Android. It might be worth a read when you want to learn more about Doze Mode, App Standby and App Standby Buckets. If you are already familiar with these concepts... well, then... this article is probably not so useful any more. :speak_no_evil:


## Uh, what is Doze Mode?

Doze Mode is a power-saving feature that has been introduced in Android 6.0 (API level 23).
The idea behind Doze Mode is that it saves power when the user doesn't use or need the phone. For example, imagine the case that the user has put down the device and walked away. One hour later, the user comes back and picks his phone up. The battery should not have dropped significantly. Right?

>Doze Mode saves energy and gets activated when the phone isn't in use

## How does Doze Mode work?

There are *two stages of Doze Mode...*

The main stage of Doze (also called *Deep Doze*) was introduced in Android 6.0 (Marshmallow) and is activated when:
- the device is not plugged into a power source, and
- the screen is locked, and
- no motion has been detected for some time (roughly 30 minutes)

When in Doze Mode the system defers battery intense actions like network calls, wake-locks, alarms (set via `AlarmManager`) to save energy. The system periodically leaves the Doze Mode for a short time (called maintenance window) to execute all deferred actions. Over time, the systems schedules maintenance windows less and less frequently.

The system leaves Doze Mode as soon as the user makes use of the smartphone by moving it, turning on the screen or connecting it to a power source.

Android Nougat *added* a more lightweight version of Doze Mode, called *Light-Doze*.
*Light-Doze* gets activated shortly after both the screen is off and the device is not plugged into a power source.
Light-Doze is pretty much the same as Deep Doze, except that it has fewer preconditions to get activated and maintenance windows are more frequently. The Doze Mode state changes from Light-Doze to Deep Doze once the conditions for Deep Doze are met.

{% include image-caption.html imageurl="/assets/images/posts/2018/doze.png" title="doze mode in android"  %}
<small>Source and Copyright: <i>developer.android.com</i> 6 August 2018, <a href="https://developer.android.com/images/training/doze.png">https://developer.android.com/images/training/doze.png</a></small>

## Restrictions of Doze Mode

Restrictions that apply when the device is in Doze Mode:
- Network calls are deferred
- Wake locks are disabled
- Scheduled alarms via `AlarmManager` are deferred to the next maintenance window
(except `setAndAllowWhileInIdle()`, `setExactAndAllowWhileIdle()`, `setAlarmClock()`)
- System doesn't scan for WiFi or GPS
- System doesn't allow execution of `JobScheduler`

At this point, you might ask...

## What about time-critical actions?

or ...

*How can I trigger time-critical actions when the device is in Doze Mode?*

In case that you want to trigger a specific action at a specific time, you can still make use of `AlarmManager`.
`setAndAllowWhileInIdle()` and `setExactAndAllowHileIdle()` get executed in time <i>even when the device is in Doze Mode.</i>
However, it is not possible to trigger a network call via a while-idle alarm when the device is in Doze Mode! A network request would lead to an `UnknownHostException`. If you want to make a network call from the background, you should use `JobScheduler` or <a href="https://www.andreasschrade.com/android-how-to-workmanager">WorkManager</a>.

In case that your time-critical action needs to get triggered from the backend, you should make use of <a href="https://firebase.google.com/docs/cloud-messaging/" target="_blank">Firebase Cloud Messaging (FCM)</a>.
By using <a href="https://firebase.google.com/docs/cloud-messaging/admin/send-messages" target="_blank">high-priority messages</a> through FCM, you can deliver and process messages immediately, even in Doze Mode. Please make only use of high-priority messages if the message is really time-critical and requires the user's immediate interaction (like a messaging app). In this case, the affected app gets temporary access to network services and partial wakelocks.

In case your app needs to be continuously active (like a navigation app), you can make use of a `ForegroundService`.
A `ForegroundService` is exempt from Doze Mode.

In rare cases that a while-idle alarm and high-priority message are not enough, it is possible to ask the user to whitelist an app. A whitelisted app can access network services and partial wakelocks even in Doze Mode.

You can request the permission `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` to prompt the user with a dialog to turn of Doze Mode optimizations for your app.


## What is App Standby?

App Standby is another power-saving functionality which has been introduced in Android 6.0 (API level 23). The idea behind that concept is, that the system maintains a list of (idling) apps that the user does not interact with very often, and limit their execution of battery draining actions (network access, background jobs).

An app is idling if the user is not interacting with (no active activities, no active foreground services) and if there are no (pending) notifications in the tray or lock screen.

An idling app gets only one maintenance window a day to run all delayed jobs and network calls. By doing so, the system can preserve as much battery power as possible.

An app leaves the idle mode when the device gets connected to a power supply; the user opens the app; the app shows a notification (e.g. via an alarm, set by using `AlarmManager`)


## What is App Standby Buckets?

App Standby Buckets is another power-saving functionality that has been introduced in Android 9. App Standby Bucket is pretty much App Standby 2.0. The idea behind that concept is that the system determines how often and how recently apps have been used. The system limits the device resources available to each app based on which bucket the app is in.

It classifies all apps in five different categories:
- Active - app is currently being used or was very recently used
- Working Set - app is used every day
- Frequent - app is often used, but not every day
- Rare - app is not frequently used
- Never - app has been installed but have never been started


## App Standby Buckets restrictions

**Active**

An app is in the active bucket if the user is currently using the app or very recently used it.

An active app doesn't have any restriction regarding scheduled jobs, alarms or push messages.

**Working Set**

An app is in the working set bucket if the user uses it often (daily) but it's currently not active.

An app in this category has mild restrictions regarding its ability to run jobs (deferred up to 2 hours) and alarms (deferred up to 6 minutes)

**Frequent**

An app is in the frequent bucket if the user uses it regularly but not on a daily basis. 
Such an app has stronger restrictions regardings its ability to run jobs (deferred up to 8 hours), alarms (deferred up to 30 minutes) and to process high-priority FCM messages (only 10 high priority messages a day).
  
**Rare**

An app is in the rare bucket if the user almost never makes use of it. Such an app has strict restrictions to run jobs (deferred up to 24 hours), alarms (deferred up to 2 hours), to process high-priority FCM messages (only 5 high priority messages a day) to access network (deferred up to 24 hours)


And finally...

*All Apps which are on the Doze Mode whitelist are exempted from these restrictions!*


If you want to figure out in which category a certain app falls into, you can make use of the setting "Standby apps" in the Developer options.

## Thank you...

... for reading this article about power-saving features in Android. If you have any question, spotted an error or want to know more about that topic, please leave a comment. I'm happy to hear from you :blush:

If you want to stay up to date with news from Android and software development in general, please follow me on <a href="https://twitter.com/andreasschrade" target="_blank">Twitter</a> a or sign up for my newsletter!