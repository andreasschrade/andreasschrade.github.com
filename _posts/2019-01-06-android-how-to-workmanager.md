---
layout: post
title: "How to WorkManager in Android"
comments: true
language: "EN"
published: false

---

## Introduction
As an Android developer, what comes first to your mind when you think about "background processing" in Android?
Maybe the concept of Service, Intent Service, Bound Service, Foreground Service, AsyncTask, LoaderManager, RxJava, Kotlin Coroutines, Executor Framework, JobScheduler, ...

Welcome to Android, a world with unlimited choices ...

This article is about another way to manage background processing: WorkManager! A new API which is part of the Android Architecture Components. The good news is that this API makes your life as an Android developer actually easier! So, let's dive right in.


## What is WorkManager?
WorkManager is one of the Android Architecture Components and is part of Android Jetpack. It replaces JobScheduler as Google’s recommended way to enqueue background task that are guaranteed to execute.

... Wait! A background task that is guaranteed to execute...?

Background Task: By default, all app components of an app run in the same thread called main thread. The main thread is also called UI thread because it is the only thread the system allows to update UI components. Background work is any app-related work that is not executed on the main thread (aka UI thread). Every time consuming task (network request, expensive processing) needs to get executed in background. Because you don't want to block the main thread.

Guaranteed Task to execute: 
This is just one of different types of background work. Background work can be categorized in two dimensions:

IMAGE
Source: (c) Google, https://www.youtube.com/watch?v=IrKoBFLwTN0

The horizontal axis represents the importance of the background work. Is it necessary that the work will be definitely executed or are there certain circumstances which makes the execution of a pending background work useless (like when leaving a screen or app)?

The vertical axis represents the timing: Is it necessary that the work will be executed at an exact time or can it be deferred?

WorkManager is on the lower right: guaranteed execution and deferrable.
Job Scheduler, Firebase JobDispatcher and the combination of AlarmManager with Broadcast receivers fall into the same category.

RxJava, Kotlin Coroutines and normal ThreadPools fall into the opposite category on the left side


Therefore, WorkManager is the right solution when you want to make sure that a background task gets - sooner or later - always processed regardless of the current app state. WorkManager tries to run the background task in a battery efficient way and by doing so it will defer the execution of tasks.

WorkManager supports Android 4.0+ (API 14+). It provides a simple interface and takes care to pick the best possible API for executing the background work depending on the configuration (API level, play services support) of the device. Internally, WorkManager uses JobScheduler on API 23+, Firebase JobDispatcher or AlarmManager with BroadcastReceiver on older API levels.

When to use WorkManager
WorkManager ensures that an enqueued task always finish. A scheduled background job even survives process death.
In general it makes sense to use WorkManager for background tasks that require a guarantee that the system will run them, even if the app exits.

Examples:
- Syncing data (a mail client could sync mails by using WorkManager)
- uploading collected user files like photos or videos
- doing time-consuming operations on data  

When not to use WorkManager
In any case which does not require a guaranteed execution or a task, which is not deferrable. 

In case that time-criticality is important, you should not use WorkManager.

In case that you need to need to run a task at an exact time (such as an alarm clock or reminder), use AlarmManager instead.

In case that you have a long running background task that needs to be executed immediately and is related to an ongoing user focussed activity (like music playback, location tracking), use ForegroundService.


What makes WorkManager beneficial?
WorkManager takes care of compatibilitiy issues and follows best practices to execute background work in a battery efficient way. Scheduled tasks even survive device restarts! (WorkManager makes use of Room and persist the current state of the task execution)

It makes it easy to
- schedule periodic tasks
- set up a dependent chains of tasks
- to parallelize work
- to set execution constraints (e.g. only execute task when network is present, when storage is not low, when battery is not low)



WorkManager API has the following building blocks:

- Worker: superclass that needs to be subclassed and contains the app logic which gets executed in background
- WorkManager: API class that allows to enqueue and access work / jobs
- WorkRequest: represents the configuration of the work request (arguments, constraints, ...)
- WorkResponse: represents information about the execution status (success, failure, ...)
- Data: Key/Value Pairs of data which gets passed to the worker


How to use WorkManager

1. Implement your own Worker by subclassing Worker and override doWork():

public class MyWorker extends Worker {

    public WorkerResult doWork() {
        
        // do your work
        return WorkerResult.SUCCESS;
    }
}

The result of doWork can succeed or fail. It is a good practice to wrap the entire method in a try-catch expression when the work being done can end with an exception.

2. Access the API via WorkManager, define the configuration and enqueue the job


Constraints constraints = new Constraints.Builder().setRequiredNetworkType(NetworkType
            .CONNECTED).build();
Data inputData = new Data.Builder()
            .build();
OneTimeWorkRequest myWork = new OneTimeWorkRequest.Builder(MyWorker.class)
            .setConstraints(constraints).setInputData(inputData).build();
WorkManager.getInstance().enqueue(myWork);

The first line initialilzes an object of type Constraints. A scheduled work will only get executed when all declared constraints are met.
It makes sense to add a "NetworkType.COÄNNECTED" constraint to an upload task, because you can't upload data unless there is a connection.

The inputData object can held necessary information that is necessary for the execution of the background work (like the file name that needs to be uploaded)

There are two main types of work requests: OneTimeWorkRequest executes the task just one time. PeriodicWorkRequest executes a task periodically.

By calling the "enqueue" method, the declared work gets enqueued and WorkManager will pick the best possible executor for the enqueued work.

It is also possible to subscribe to the current job state by using Live Data:
WorkManager.getInstance().getStatusById(myWork.getId()).observe(this,
        workStatus -> {
    if(workStatus!=null && workStatus.getState().isFinished()){
             }
});

It is also possible to schedule periodic work:
Constraints constraints = new Constraints.Builder().setRequiredNetworkType
        (NetworkType.CONNECTED).build();
PeriodicWorkRequest myWork = new PeriodicWorkRequest.Builder(MyWork
        .class, 15, TimeUnit.MINUTES).addTag(MyWork.TAG)
        .setConstraints(constraints).build();
WorkManager.getInstance().enqueue(myWork);

It is also possible to create a chain of jobs...
In a sequence:
WorkManager.getInstance(this)
        .beginWith(Work.from(PrepareUploadWorker.class))
        .then(Work.from(UploadWorker.class))
        .enqueue();

In parallel:
WorkManager.getInstance(this).enqueue(Work.from(MyWorker1.class, 
        MyWorker2.class));

Observing work:


Unique work:
In general there are two types in starting a background task:
- beginWith(): This method begins a chain of one or more OneTimeWorkRequests. Such a background task get enqueued and executed regardless if there is any other background task already running or enqueued of the same type.
- beginUniqueWork(): This method allows you to begin unique chains of work for situations where you only want one chain with a given name to be active at a time. (e.g. a global sync task). In case that there is already one pending, you can choose to let it run (ExistingWorkPolicy.KEEP) or replace it (ExistingWorkPolicy.REPLACE) with your new background task or to append the new sequence to the existing one as a child of all leaf nodes (ExistingWorkPolicy.APPEND)

 val workManager = WorkManager.getInstance()

      workManager.beginUniqueWork(UNIQUE_WORK_NAME, ExistingWorkPolicy.REPLACE, cleanFiles)
          .then(applySepiaFilter)
          .then(zipFiles)
          .then(uploadZip)
          .enqueue()


Cancel work:
WorkManager allows you to cancel work in several ways:

Unique work: If you started unique work with a unique work name, then you can just call cancelUniqueWork() and pass the name as parameter.
By tag: When you tag one or more WorkRequests, you call cancelAllWorkByTag.
By ID: If you have the ID of the WorkRequest and want to cancel it, you call cancelWorkById().
Since you're using unique work, you'll cancel it using the unique work name.



WorkManager is in beta now. Once WorkManager is released as stable, it will be the preferred way to run background tasks  

Thank you for reading this article about WorkManager in Android. If you have amy question, spotted an error or simply want to know more about WorkManager, please leave a comment. I am happy to hear from you.

If you want to stay up to date with news from Android and software development in general, please follow me on Twitter or sign up for my weekly Newsletter!
