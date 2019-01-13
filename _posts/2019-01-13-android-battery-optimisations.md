---
layout: post
title: "Battery optimisation techniques in Android"
comments: true
language: "EN"
published: false

---

## Doze Mode

Doze Mode is a power-saving feature that has been introduced in Android 6.0 (API level 23).
The idea behind Doze Mode is that is saves power when the user doesn't use or need the phone. For example, imagine the case that the user has put down the device and walked away. One hour later, the user comes back and picks his phone up. The battery should not have dropped significantly. Right?

To sum it up:

>Doze Mode saves energy and it gets activated when the phone is not in use

The phone is not in use when:
- it's not plugged in to a power source, and
- the screen is locked, and
- no motion has been detected for some time

When in Doze Mode the system defers battery intense actions like network calls, wake-locks, alarms (set via AlarmManager). The system periodically leaves the Doze Mode for a short time (called maintenance window) to execute all deferred actions. Over time, the systems schedules maintenance windows less and less frequently.

The system leaves Doze Mode as soon as the user makes use of the smartphone by moving it, turning on the screen or connecting it to a power source.

Restrictions that apply when the app is in Doze Mode:
- Network calls are deferred
- Wake locks are disabled
- Scheduled alarms via AlarmManager are deferred to the next maintenance window
(exceptions: the following methods will wake up the device even if it is in Doze Mode:
setAndAllowWhileInIdle() or setExactAndAllowWhileIdle() or setAlarmClock())
- System doesn't scan for WiFi
- System doesn't allow execution of JobScheduler

At this point, you might ask, how can I trigger time-critical actions?
In case that you want to trigger a specific action at a specific time, you can still make use of AlarmManager.
setAndAllowWhileInIdle() and setExactAndAllowHileIdle get executed in time even when the device is in Doze Mode.

In case that the action needs to get triggered from the backend, you should make use of Firebase Cloud Messaging (FCM).
By using high-priority messages through FCM, you can deliver and process them immediately, even in Doze Mode.


## Conclusion

Thank you for reading this article about power-saving features in Android. If you have any question, spotted an error or want to know more about that topic, please leave a comment. I'm happy to hear from you :blush:

If you want to stay up to date with news from Android and software development in general, please follow me on <a href="https://twitter.com/andreasschrade" target="_blank">Twitter</a>a or sign up for my newsletter!