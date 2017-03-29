## Foreword

Let's start this article with three bullet-points:
- Android developers are busy most of the time. They do not want to read very looooong articles.
- Threading in Android is very, very important. Period.
- Bullet-Points are pretty cool

This article has been written in respect to the bullet points above: <b>The article is as short as possible and contains as much as possible information about threading in Android.</b>


## Architecture
- Android is based on Linux which supports
	- multitasking: performing multiple tasks (aka processes) over a certain period of time by executing them concurrently
	- multithreading: performing multiple threads within the context of one process
- The execution of apps is based on Linux processes
- A Linux process is started for every new app
- Android can run multiple apps at the same time
- Every Linux process contains:
	- a runtime: Dalvik or ART (Android Runtime)
	- a running app within the runtime
- Every Linux process can execute the app code via multiple threads
- A scheduler determines what thread the CPU should process and for how long

<!--
TODO - Abbildung, die die Beziehung mehrer prozesse, inklusive runtime, app und threads erkärt mit beispielen
>z.B. Runtime (ART), Process (Gmail), Thread (Main-Thread)
Fig 1-3 
-->

Note: It is also possible but very rare that...
- an app runs in several processes
- several apps run in the same process


## Application Lifecycle
### Application start
General:
- The Android system starts a new Linux process for an app when an app component (Activity, Service, Content Provider, Broadcast Receiver) needs to be started and the app does not have a running process yet.
- All app components of the same app run in the same process and thread (main thread) -> It is possible to change this default behaviour!
- If an app component starts and there already exists a process for that app, then the new component is started within that already existing process

App startup sequence:
1. Start Linux process
2. Create runtime: Dalvik or ART (Android Runtime)
3. Create Application instance (android.app.Application.onCreate() is called)
4. Create the component for the application (e.g. Activity, Service, ...) - It is the component that needs to be executed and triggered the startup


### Application end
Android kill the process when:
- it's no longer needed
- the system must recover memory for other apps

Important:
There's no guarantee that onDestroy() (Activity, Service) will be called

## Main Thread aka UI Thread
General:
- By default, all app components of the same app run in the same thread, called main thread
- The main thread is also called UI thread because it is the only thread the system allows to update UI components

Responsiveness:
Always ensure that no long-running tasks are executed on the UI thread!
A long running task will block other sensitive tasks on the UI thread (e.g. animations)

Example:
Let's consider a simple view animation:
- Animations are updated in an event loop where every event updates the animation with one frame 
- An event is just a very small change (e.g. move view by 1px)
- The more events/drawing changes that can be executed per time frame, the better the animation is perceived
- 60 drawing changes per seconds (60 fps) is the ultimative goal. Result: Every frame has to render within 16 ms (1000 ms / 60 frames = 16,66 ms)
- If another task is running on the UI thread simultaneously, both the drawing cycle and the "blocking" task have to finish within 16 ms to avoid a stuttering animation

The calculation is a bit simplified. However, be cautious about possible long running tasks everytime.


## Service
### General
- the Service runs in the UI thread! 
- only the first start request creates and starts the Service. Consecutive start requests just pass on the Intent to the started Service (onStartCommand)
- the Service lifecycle does not control the lifetime of background threads!
- a Bound Service keeps a reference counter on the number of bound components. When the reference counter is decremented to zero, the Service is destroyed
- a foreground service has the same priority as an active activity. It <i>should</i> (but you never know... ) not be killed by the Android system, even if is low on memory

### Lifecycle
Start:
A service starts...
- with a call to context.startService(Intent) - Regular Service
- with a call to context.bindService(Intent, ServiceConnection, int) - Bound Service

End:
A service ends...
- with a call to context.stopService(Intent)
- with a call to service.stopSelf()
- the system must recover memory for other apps

## IntentService
### General:
- executes tasks sequentially
- runs in the background! No need to use Thread, HandlerThread...
- excellent for "Fire-and-Forget" tasks
- not possible to cancel tasks from the outside

### Lifecycle
Start:
- an IntentService starts with a call to context.startService(Intent)
- if the IntentService is already running, the new task is queued (FIFO)

End:
- the IntentService stops itself automatically when there are no more Intents to process (No need to call stopSelf()!)
- the system must recover memory for other apps 

## AsyncTask
### General
- convenient way to perform long operations (a few seconds) from within an Activity
- computation runs on a background thread and whose result is published on the UI thread
- a task can be executed only once!
- tasks are executed sequentially on a single thread! If you want parallel execution, you have to use asyncTask.executeOnExecutor(THREAD_POOL_EXECUTOR, args)
- does not handle configuration changes automatically
- AsyncTask instance must be created on the UI thread.

### Lifecycle
Start:
- an AsyncTask starts with a call to asyncTask.execute(Params, Progress, Result);

End:
- when the AsyncTask finished the work
- by calling asyncTask.cancel(false). cancel(false) does not automatically cancel the task execution. The implementation of doInBackground(Params...) has to check periodically the return value of isCancelled().

## Message Passing
- nonblocking consumer-producer pattern

### Parts:
Looper:
- message dispatcher associated with the consumer thread
- A thread can only have one Looper at the same time

Handler:
- consumer thread message handler
- A Looper can have many associated handlers. All handlers insert messages into the same queue

MessageQueue
- Unbounded linked list of messages to be processed on the consumer thread
- A Looper has only one MessageQueue

Message
- Container object carrying either a data item or a task
- Inserted by producer threads
- Consumed by consumer thread
- Looper runs in the consumer thread, retrieves messages from the MessageQueue in a sequential order

