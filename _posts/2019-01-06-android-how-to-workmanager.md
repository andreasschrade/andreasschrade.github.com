---
layout: post
title: "How to WorkManager in Android"
comments: true
language: "EN"
published: false

---


## What is WorkManager?
WorkManager is one of the Android Architecture Components and is part of Android Jetpack. 

It aims to improve the developer experience by providing a simple wrapper that handles background processing. It is intended for background jobs that gets executed regardless if the app is in foreground or not.

Internally, WorkManager takes care to pick the best possible API (JobScheduler - on API 23+, Firebase JobDispatcher, AlarmManager with BroadcastReceiver)
In case that the app is still in foreground, WorkManager will even try to do the work in the app's process.

WorkManager supports Android 4.0+ (API 14+)

When to use WorkManager
WorkManager ensures that an enqueued task always finish. A scheduled background job even survives process death.
In general it makes sense to use WorkManager for background tasks that require a guarantee that the system will run them, even if the app exits. WorkManager tries to run the background task in a battery efficient way and by doing so it will defer the execution of tasks. In case that time-criticality is important, you should not use WorkManager.
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

- Worker: contains the app logic which should get executed in background
- WorkManager: API class that allows to enqueue and access work / jobs
- WorkRequest: represents the configuration (arguments, constraints, ...) of the work
- WorkResponse: represents information about the execution (success, failure, ...)
- Data: Key/Value Pairs of data which gets passed to the worker


How to use WorkManager

1. Implement your own Worker by subclassing Worker and implementing doWork():

public class MyWorker extends Worker {

    public WorkerResult doWork() {
        
        // do your work
        return WorkerResult.SUCCESS;
    }
}


2. Access the API via WorkManager, define the configuration and enqueue the job

Constraints constraints = new Constraints.Builder().setRequiredNetworkType(NetworkType
            .CONNECTED).build();
Data inputData = new Data.Builder()
            .build();
OneTimeWorkRequest myWork = new OneTimeWorkRequest.Builder(MyWorker.class)
            .setConstraints(constraints).setInputData(inputData).build();
WorkManager.getInstance().enqueue(myWork);


WorkManager will take care of picking the best possible executor for the enqueued work. This might either be JobScheduler, JobDispatcher, AlarmManager or Executor.


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


WorkManager is in beta now. Once WorkManager is released as stable, it will be the preferred way to run background tasks  

Thank you for reading this article about WorkManager in Android. If you have amy question, spotted an error or simply want to know more about WorkManager, please leave a comment. I am happy to hear from you.

If you want to stay up to date with news from Android and software development in general, please follow me on Twitter or sign up for my weekly Newsletter!
