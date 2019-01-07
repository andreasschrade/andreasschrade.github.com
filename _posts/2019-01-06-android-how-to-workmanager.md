---
layout: post
title: "Android WorkManager: A short Tutorial"
comments: true
language: "EN"

---

As an Android developer, what comes first to your mind when you think about _background processing_ in Android?
Maybe the concept of Service, Intent Service, Bound Service, Foreground Service, AsyncTask, LoaderManager, HandlerThread, RxJava, Kotlin Coroutines, Executor Framework, JobScheduler, ... much pain?! :scream_cat:

> "So many roads. So many detours. So many choices. So many mistakes." ― Sarah Jessica Parker ―

This article introduces another way to manage background processing: `WorkManager` - A new API which is part of the Android Architecture Components. The good news is that this API makes your life as an Android developer easier! Sounds good? Ok so, let's dive right in...

## What is WorkManager?
WorkManager is one of the Android Architecture Components and is part of Android Jetpack. It replaces `JobScheduler` as Google’s recommended way to enqueue background work that needs a <u>combination of opportunistic and guaranteed execution.</u>

<i>... <b>Wait!</b> A background work that needs a combination of <u>opportunistic</u> and <u>guaranteed execution</u>?</i>

What does this mean?

<table style="background:#ffffee">
<tr><td>
<u>Background Work:</u>
</td></tr>
<tr><td>
 Background work is any app-related work that doesn't get executed on the main thread (aka UI thread). Every time-consuming task (network request, expensive processing) needs to get executed in the background because you don't want to block the UI thread.
</td></tr>
<tr><td>
<u>Opportunistic execution:</u>
</td></tr>
<tr><td>
This means that WorkManager executes your background work as soon as it can but it can defer the execution of tasks to work more battery efficient.
</td></tr>
<tr><td>
<u>Guaranteed execution:</u>
</td></tr>
<tr><td>
WorkManager takes care that your background work gets executed (even if the user navigates away from the app or triggers a reboot)
</td></tr>
</table>

In general, there are different types of background work. If you want to learn more about that, please see [appendix A](#appendix-a)

So...

*`WorkManager` is the right solution when...*

1. ... you want to run a time or CPU intensive task in the background and,
2. ... you want to make sure that the task <u>always finish</u> (regardless of the current app state, process state or user behaviour) => Scheduled work even survives process death and device reboot!
3. .. the task is <u>not time-critical</u> because the execution might get deferred to achieve battery efficiency 

Examples:
* Syncing data (a mail client could sync emails by using WorkManager)
* uploading collected user files like photos or videos
* doing time-consuming operations on data  


*`WorkManager` is **NOT** the right solution when...*
* ... you don't need a guaranteed execution -> when it simply doesn't make sense to finish an enqueued task in certain circumstances (for example a background task that is bound to a certain screen doesn't need to get executed once the user left the app)
* ... you want to execute a non-deferrable task (time-criticality) 
* ... you want to execute the task at an exact time (such as an alarm clock or reminder) -> use `AlarmManager` instead
* ... you have a long-running background task that needs to be executed immediately and is related to an ongoing user focussed activity (like music playback, location tracking) -> use `ForegroundService` instead


### What makes WorkManager beneficial?
WorkManager takes care of compatibility issues and follows best practices to execute background work in a battery efficient way. It supports Android 4.0+ (API 14+) and provides a simple and flexible API. 

The WorkManager API makes it easy to
* schedule periodic tasks
* schedule unique (singleton) tasks
* set up dependent chains of tasks
* parallelize work
* set execution constraints (e.g., only execute the task when the network is present, when storage is not low, when the battery is not low)

WorkManager takes care to pick the best possible API for executing the background work depending on the device configuration (API level, play services support). Internally, `WorkManager` delegates the work to one of the following three APIs: 
* `JobScheduler` (API 23+)
* Firebase `JobDispatcher` (below API 23 and in case play services are available)
* `AlarmManager` with `BroadcastReceiver` (fallback)

## How to use WorkManager
The WorkManager API has the following building blocks:
* `Worker`: superclass that needs to be subclassed and contains the code for the actual work you want to perform in the background
* `WorkManager`: API class that allows enqueueing and accessing work
* `WorkRequest`: represents the configuration of the work request (arguments, constraints, ...)
* `WorkResponse`: represents information about the execution status (success, failure, ...)
* `Data`: Key/Value Pairs of data which gets passed to the worker

### Example

*1. Add WorkManager to your app*

<pre><code class="language-kotlin">
dependencies {
   // WorkManager kotlin dependency
    implementation "android.arch.work:work-runtime-ktx:1.0.0-beta01"
    // Test helper
    androidTestImplementation "android.arch.work:work-testing:1.0.0-beta01"
}
</code></pre>


*2. Implement your own Worker by subclassing Worker and override doWork():*

<pre><code class="language-kotlin">
import android.content.Context
import android.util.Log
import androidx.work.Worker
import androidx.work.WorkerParameters
import java.util.concurrent.TimeUnit.SECONDS

class ExampleWorker(
    context: Context,
    workerParams: WorkerParameters
) : Worker(context, workerParams) {
    override fun doWork(): Result {
        // perform long running operation
        for (i in 1..10) {
            SECONDS.sleep(1)
            Log.d("ExampleWorker", "progress: $i")
        }
        return Result.success()
    }
}
</code></pre>

The result of the doWork() can either be:
* `Result.success()` -> task sucessfully finished
* `Result.failure()` -> task failed and no need to retry
* `Result.retry()` -> task failed; WorkManager will retry the execution based on the backoff policy

It is a good practice to wrap the entire method in a try-catch expression when the work can end with an exception.

*3. Access the API via WorkManager, define the configuration and enqueue the job*

<pre><code class="language-kotlin">
val constraints = Constraints.Builder()
    .setRequiredNetworkType(NetworkType.CONNECTED)
    .build()
val inputData = Data.Builder()
    .putString("example_key", "example_value")
    .build()
val myWork = OneTimeWorkRequest.Builder(ExampleWorker::class.java)
    .setConstraints(constraints)
    .setInputData(inputData)
    .build()
WorkManager.getInstance().enqueue(myWork)
</code></pre>

The first line initializes an object of type `Constraints`. As soon as all declared constraints are met, a scheduled work gets ready for execution.
It makes sense to add a `NetworkType.CONNECTED` constraint to an upload task, because you can't upload data unless there is a connection.

The `inputData` object holds necessary information that is required for the execution of the background work (like the file name that needs to be uploaded). Please be careful that you don't exceed 10KB...Otherwise, you encounter an `IllegalStateException` at runtime.

There are two main types of work requests:
1. OneTimeWorkRequest executes the task just one time
2. PeriodicWorkRequest executes a task periodically

A created WorkRequest has an associated ID (UUID). It is also possible to set a tag for a `WorkRequest` instance. That makes it easier to access the scheduled work later.

By calling the `enqueue` method, the declared work gets enqueued and `WorkManager` picks the best possible executor for the enqueued work. That's it! :sunglasses: :rocket:


### Observing the job state by using Live Data

WorkManager makes it easy to observe the current state of the execution:

<pre><code class="language-kotlin">
WorkManager.getInstance()
	.getWorkInfoByIdLiveData(id!!)
	.observe(this, Observer { status ->
	    if (status?.state?.isFinished == true) {
	        // job finished
	    }
	})
</code></pre>

A scheduled work has one of the following statuses:

* `ENQUEUED` - WorkRequest is enqueued and eligible to run when its Constraints are met and resources are available
* `RUNNING` - WorkRequest is currently being executed
* `SUCCEEDED` - WorkRequest has successfully completed (a PeriodicWorkRequest can't enter this state and will simply go back to ENQUEUED)
* `FAILED` - WorkRequest has failed
* `BLOCKED` - WorkRequest is currently blocked because its prerequisites haven't finished successfully.
* `CANCELLED` - WorkRequest has been canceled


### Schedule periodic background work

The following example sets up a `PeriodicWorkRequest` to run every 15 minutes.

<pre><code class="language-kotlin">
val work = PeriodicWorkRequest.Builder(
    ExampleWorker::class.java, 15, TimeUnit.MINUTES)
    .addTag("MyTag")
    .build()
WorkManager.getInstance().enqueue(work)
</code></pre>

A tag is optional but makes it easier to manage a periodic background work.

### Schedule a chain of background jobs

In a sequence:

<pre><code class="language-kotlin">
WorkManager.getInstance()
    .beginWith(OneTimeWorkRequest.from(PrepareUploadWorker::class.java))
    .then(OneTimeWorkRequest.from(UploadWorker::class.java))
    .enqueue()
    </code></pre>
At first, PrepareUploadWorker gets executed, then UploadWorker.

In parallel:
<pre><code class="language-kotlin">
WorkManager.getInstance()
    .enqueue(
        listOf(
            OneTimeWorkRequest.from(ExampleWorker1::class.java),
            OneTimeWorkRequest.from(ExampleWorker2::class.java)
        )
    );
</code></pre>

Both tasks get enqueued at the same time and can (depends on the internal scheduling) run at the same time without any dependency.


### Unique work

In many cases, it makes sense to have only one job of a particular type running at the same time (like a singleton job).
For example, it doesn't make sense for a mail client to have multiple synchronization jobs running at the same time.

`beginUniqueWork()` allows you to have a unique chain of work with a given name to be active at a time! You can decide what should happen when there is already one job of the same type pending:

* `ExistingWorkPolicy.KEEP` - let it run
* `ExistingWorkPolicy.REPLACE` - replace the existing one with the new background task
* `ExistingWorkPolicy.APPEND` - append the new sequence to the existing one as a child of all leaf nodes

`beginWith()` always enqueues and executes the chain of work regardless if there is any other background task of the same type already running or enqueued.

<pre><code class="language-kotlin">
WorkManager.getInstance()
	.beginUniqueWork(UNIQUE_JOB_NAME, ExistingWorkPolicy.REPLACE, cleanSync)
	.then(sync)
 	.then(postProcessSync)
  	.enqueue()
 </code></pre>

### Cancel work

WorkManager allows you to cancel work in several ways:
* `cancelAllWork()` - cancels all unfinished work from the entire codebase (all modules, libraries)
* `cancelAllWorkByTag(tag)` - cancels all unfinished work with the passed tag name
* `cancelUniqueWork(uniqueWorkName)` - cancels all unfinished unique work with the passed name
* `cancelWorkById(uuid)` - cancels unfinished work with the given work ID (UUID - status.getId())

## Conclusion

WorkManager makes background processing in Android easier! It has a consistent, powerful but still simple API. Once WorkManager is released as stable, it will be the preferred way to run background tasks.


Thank you for reading this article about WorkManager in Android. If you have any question, spotted an error or want to know more about WorkManager, please leave a comment. I'm happy to hear from you :blush:

If you want to stay up to date with news from Android and software development in general, please follow me on <a href="https://twitter.com/andreasschrade" target="_blank">Twitter</a>a or sign up for my newsletter!



<h2 id="appendix-a"> Appendix A</h2>

Background work can be categorized into two dimensions:

{% include image-caption.html imageurl="/assets/images/posts/2018/workmanager.jpg" title="different types of background processing"  %}

The _horizontal axis_ represents the importance of the background work. Is it necessary that the work will be definitely executed or are there certain circumstances which make the execution of a pending background work useless (like when leaving a screen or app)?

The _vertical axis_ represents the timing: Is it necessary that the work will be executed at an exact time or can it be deferred?

WorkManager is on the lower right: guaranteed execution and deferrable.
`Job Scheduler`, `Firebase JobDispatcher` and the combination of `AlarmManager` with `Broadcast receivers` fall into the same category.

RxJava, Kotlin Coroutines and normal ThreadPools fall into the opposite category on the left side