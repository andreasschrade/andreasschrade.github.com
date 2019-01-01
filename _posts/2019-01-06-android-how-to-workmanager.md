---
layout: post
title: "How to WorkManager in Android"
comments: true
language: "EN"
published: false

---


## What is WorkManager?
WorkManager is one of the Android Architecture Components and is part of Android Jetpack. 

WorkManager is the preferred API that helps to run deferrable background work that needs to run when various constraints are met. In this case “background work” is defined as work that gets executed regardless of the current state of the app. (app is in foreground, app is in background) 
To clarify: scheduled background survives process death!

In case that you need to need to run a task at an exact time (such as an alarm clock or reminder), use AlarmManager.

In case that you have a long running background task that needs to be executed immediately and is related to an ongoing user focussed activity (music playback), use ForegroundService.

WorkManager is in beta now. Once WorkManager is released as stable, it will be the preferred way  to run background tasks  

Thank you for reading this article about WorkManager in Android. If you have amy question, spotted an error or simply want to know more about WorkManager, please leave a comment. I am happy to hear from you.

If you want to stay up to date with news from Android and software development in general, please follow me on Twitter or sign up for my weekly Newsletter!
