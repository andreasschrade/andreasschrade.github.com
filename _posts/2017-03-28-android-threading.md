---
layout: post
title: "Threading in Android - A TLDR"
comments: true
language: "EN"


---


## Foreword

Let's start this article with three bullet-points:

- Android developers are busy most of the time. They do not want to read very looooong articles.
- Threading in Android is very, very important.
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
- Each process has its own memory space and communicates with each other through interprocess communication (IPC) 
- A scheduler determines what thread the CPU should process and for how long

<img style="display:block; margin: 0 auto" alt="Android process architecture" src="{{ site.url }}/assets/android-threading-process-architecture.png">

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
Android kills the process when:

- it's no longer needed
- the system must recover memory for other (more important) apps

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
- An event is just a very small change (e.g. move view element by 1px)
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
- the system must recover memory for other (more important) apps

### Example
{% highlight java %}
public class MyService extends Service {
   @Override
   public int onStartCommand(Intent intent, int flags, int startId) {
      return MY_START_MODE;
   }

   @Override
   public IBinder onBind(Intent intent) {
      return null;
   } 
}
{% endhighlight %}

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
- the system must recover memory for other (more important) apps 


### Example
{% highlight java %}
public class MyIntentService extends IntentService {
    public static final String MY_PARAM = "param";

    public MyIntentService() {
        super("MyIntentService");
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        String msg = intent.getStringExtra(MY_PARAM);
        // do something
    }
}
{% endhighlight %}

## AsyncTask

### General

- allows to run code in the background and to synchronize again with the main thread
- excellent for short background operations which need to update the user interface
- convenient way to perform long operations (a few seconds) from within an Activity
- computation runs on a background thread and whose result is published on the UI thread
- a task can be executed only once
- tasks are executed sequentially on a single thread! If you want parallel execution, you have to use asyncTask.executeOnExecutor(THREAD_POOL_EXECUTOR, args)
- does not handle configuration changes automatically (common solution: use a retained headless fragment)
- AsyncTask instance must be created on the UI thread.

### Lifecycle
Start:

- an AsyncTask starts with a call to asyncTask.execute(Params, Progress, Result);

End:

- when the AsyncTask finished work
- by calling asyncTask.cancel(false). The implementation of doInBackground(Params...) has to check periodically the return value of isCancelled().

### Example

{% highlight java %}
public class MyActivity extends Activity {
	private TextView textViewStatus;
    public void onCreate(Bundle savedInstanceState) {
           // init...
           MyAsyncTask task = new MyAsyncTask();
           task.execute(urls); // e.g download urls
    }

	private static class MyAsyncTask extends AsyncTask<String, Void, String> {
	        @Override
	        protected String doInBackground(String... urls) {
	                // download something via url and return result
	                return downloadResultAsString; // e.g. "lorem ipsum"
	        }

	        @Override
	        protected void onPostExecute(String result) {
	                textViewStatus.setText(result); // sets "lorem ipsum" to TextView textViewStatus
	        }
	}
}
{% endhighlight %}

## Message Passing
Scenario:
"We are running on a client thread and want to have code executed in another thread context"

### Message Passing Parts:

General:

- A Thread can have exactly one Looper (the UI thread does automatically have a Looper)
- A Looper has exactly one MessageQueue
- A MessageQueue can have many Messages
- A Handler is bound to a Thread via Looper
- Multiple Handler instances can be bound to the same Thread (via Looper)

<img style="display:block; margin: 0 auto" alt="Android threading handler relation" src="{{ site.url }}/assets/android-threading-handler-relation.png">

Process:

1. The producer thread inserts a Message into the MessageQueue by using a Handler which is connected to the consumer Thread
2. The Looper runs in the consumer Thread, retrieves the Message from the MessageQueue and "sends" the Message to the Handler which is still connected to the consumer Thread
3. The Handler executes the Message via handleMessage(Message)


<img style="display:block; margin: 0 auto" alt="Android threading handler relation" src="{{ site.url }}/assets/android-threading-handler-process.png">

More Details:

Thread:

- A Thread can have only one Looper. A Looper can have many unique Handlers associated with it
- A Thread gets a Looper and MessageQueue by calling Looper.prepare() after its running

MessageQueue:

- Unbounded linked list of messages to be processed on the consumer thread
- Holds the list of messages to be dispatched by a Looper
- A Looper has only one MessageQueue
- Messages are added through Handler objects associated with the Looper

Message:

- Container object carrying either a data item or a task (Runnable)
- Inserted by producer threads
- Consumed by consumer thread

Looper:

- Message dispatcher associated with the consumer thread
- A thread can only have one Looper at the same time
- Looper runs in the consumer thread
- Looper is a message handling loop
- Looper retrieves messages from the MessageQueue in a sequential order

Handler:

- consumer thread message handler
- A Handler is bound to a specific Thread and its Looper (and MessageQueue)
- Handler is responsible for adding, removing, dispatching messages of the associated thread's MessageQueue
- A Looper can have many associated handlers. All handlers insert messages into the same queue


### Example

{% highlight java %}
class MyLooperThread extends Thread {
    public Handler handler;

    public void run() {
        // Initialize a Looper and associate it with the current thread
        Looper.prepare();
        handler = new Handler() {
            @Override public void handleMessage(Message msg) {
                // handle incoming message
            }
        };
        // Starts the Looper
        Looper.loop();
    }
}
{% endhighlight %}
{% highlight java %}
class MainActivity extends Activity {
	public void onCreate(...) {
		// Other stuff
		MyLooperThread myThread = new MyLooperThread();
		myThread.start();

		// much later, send a message
		Message msg = myThread.handler.obtainMessage(0);
        myThread.handler.sendMessage(msg);
	}
}
{% endhighlight %}


## HandlerThread

### General

- Just a "normal" Thread which sets up the internal message passing mechanism automatically => No need to init a Looper manually...
- Applicable to many background execution use cases, where sequential execution and control of the message queue is desired
- Use the constructor with the name argument -> simplifies debugging
- Started in the same way as a Thread -> start()
- Terminated either with quit() or quitSafely()
	- quit() - Terminates the Looper without processing any more messages in the MessageQueue
	- quitSafely() - Similar to the previous version but makes sure that the pending messages that are due to be delivered are handled

### Example

{% highlight java %}
HandlerThread myThread = new HandlerThread("MyHandlerThread");
myThread.start();
Handler handler = new Handler(myThread.getLooper()) {
	@Override
	public void handleMessage(Message msg) {
		super.handleMessage(msg);
		// TODO: process message
	}
};
{% endhighlight %}

## Loaders

### General

- framework to run asynchronous operations with content providers or other data sources
- loads data asynchronously
- callback when content changes or is added to the data source
- accessible via Activity or Fragment
- supports configuration change 


## Summary
- use HandlerThread instead of "normal" Java Thread with manual Looper setup
- use AsyncTask for short(!) (repetitive) tasks that are tightly <i>bound to the UI</i>
- use IntentService for repetitive and/or long tasks that should run on the <i>background</i>, independent of the UI
- use Service for very short tasks without UI; for longer tasks use threads within Service


