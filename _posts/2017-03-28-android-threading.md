---
layout: post
title: "Threading in Android"
comments: true
language: "EN"


---


## Foreword

Devs are busy most of the time and don't have time to read very long articles. This article is as short as possible and contains as much as possible information about threading in Android. Let's start!


## Architecture
### Multitasking and Multithreading
- Android is based on Linux and supports:
	- multitasking: performing multiple tasks (aka processes) over a certain period of time by executing them concurrently
	- multithreading: performing multiple threads within the context of one process

### Processes
- The execution of apps is based on Linux processes
- A Linux process is started for every new app
- Android can run multiple apps at the same time (each app runs in its own process)
- Every process contains:
	- a runtime: Dalvik or ART (Android Runtime)
	- a running app within the runtime
- Each process has its own memory space and communicates with each other through interprocess communication (IPC) 
- A scheduler determines what thread the CPU should process and for how long

<img style="display:block; margin: 0 auto" alt="Android process architecture" src="{{ site.url }}/assets/android-threading-process-architecture.png">

<strong>Note:</strong> It is also possible but very rare that...

- an app runs in several processes
- several apps run in the same process


## Application Lifecycle
### Application start
General:
- Android starts a new Linux process for an app when an app component (Activity, Service, Content Provider, Broadcast Receiver) needs to be started and the app does not have a running process yet
- All app components of the same app run in the same process and thread (main thread) → default behavior
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

<u>Important:</u> <br>
There's <i>no guarantee</i> that <i>onDestroy()</i> (Activity, Service) will be called

## Memory-Management

### General

- Processes are kept alive in memory for as long as possible
- Processes are killed when memory is needed for the more important processes

### Process Hierarchy

- Android kills processes (not components!) to release resources
- Processes with the lowest importance are killed first
- Android can re-launch an app (the process) based on its last state when the process was killed

Process importance (from high to low):

- Foreground Processes
- Visible Processes
- Service Processes
- Background Processes
- Empty Processes

<u>Foreground Process:</u>

- Process that has components that the user is interacting with like
	- a visible and active component (Activity)
	- a Service is bound to an Activity in front in a remote process (same with Content Providers)
	- a foreground Service
	- a running BroadcastReceiver

<u>Visible Process:</u>

- A process that has visible activities which are not in the foreground (= partly obscured)
- dependent processes are not prematurely killed while the activity is visible (a bound service or content providers gets the same process status as the activity which still lives)

<u>Service Process:</u>

- has one or more started Services which are not interacting with a visible component or foreground component
- common state for most background services, the process is only killed if there is much memory pressure

<u>Background Process:</u>

- contains activities that are not visible
- Android kills processes of this type in order of least recent usage (oldest is reclaimed first)

<u>Empty Process:</u>

- don't have any active app components
- can be easily killed at any point
- do only exist to improve start-up time when a component needs to be (re-)activated

<u>Important:</u><br>
Priorities are done at the <i>process level</i> and not the component level. A single component can push the entire process into the foreground level.

## Thread Scheduling

<strong>A thread scheduler decides which threads in the Android system should run, when, and for how long</strong>

Android’s thread scheduler uses two main factors to determine the scheduling:

- Niceness Values
- Control Groups (Cgroups)

### Niceness Values

- a thread with a higher niceness value will run less often than those with a lower niceness value (this sounds paradoxical)
- niceness value has the range of -20 (most prioritized) to 19 (least prioritized); default value is 0
- a new Thread inherits its priority from the thread where it is started
- it is possible to change the priority via:
	- <i>thread.setPriority(int priority)</i> - values: 0 (least prioritized) to 10 (most prioritized)
	- <i>process.setThreadPriority(int priority)</i> - values: -20 (most prioritized) to 19 (least prioritized)

### Control Groups (Cgroups)

- Android has multiple control groups. The most important are:
	- the Foreground Group
	- the Background Group
- every thread belongs to a thread control group (e.g. Foreground Group)
- threads in the different control groups are allocated different amounts of CPU execution time
- threads in the Foreground Group receive a lot more execution time than threads in the Background Group
- if an application runs at the Foreground or Visible process level (see above), the threads created by that application will belong to the Foreground Group
- all threads belonging to applications which are not currently running in the foreground are implicitly moved to the Background Group


## Main Thread aka UI Thread
General:

- By default, all app components of the same app run in the same thread called main thread
- The main thread is also called UI thread because it is the only thread the system allows to update UI components

<u>Responsiveness:</u><br>
<i>Always ensure that no long-running tasks are executed on the UI thread!</i><br>
A long running task will block other sensitive tasks on the UI thread (e.g. animations)

<u>Example:</u>
Let's consider a simple view animation:

- Animations are updated in an event loop where every event updates the animation with one frame 
- An event is just a very small change (e.g. move view element by 1px)
- The more events/drawing changes that can be executed per time frame, the better the animation is perceived
- 60 drawing changes per seconds (60 fps) is the ultimate goal. Result: Every frame has to render within 16 ms (1000 ms / 60 frames = 16,66 ms)
- If another task is running on the UI thread simultaneously, both the drawing cycle and the "blocking" task have to finish within 16 ms to avoid a stuttering animation

The calculation is a bit simplified. However, be cautious about possible long running tasks.


## Service

<strong>Use Service for very short tasks without UI; for longer tasks use threads within Service</strong>

### General

- the Service runs in the UI thread! 
- only the first start request creates and starts the Service. Consecutive start requests just pass on the Intent to the started Service (onStartCommand)
- the Service lifecycle does not control the lifetime of background threads!
- a bound Service keeps a reference counter on the number of bound components. When the reference counter is decremented to zero, the Service is destroyed
- a foreground service has the same priority as an active activity. It <i>should</i> (but you never know... ) not be killed by the Android system, even if is low on memory

### Lifecycle
<u>Start:</u>
A service starts...

- with a call to <i>context.startService(Intent)</i> - Regular Service
- with a call to <i>context.bindService(Intent, ServiceConnection, int)</i> - Bound Service

<u>End:</u>
A service ends...

- with a call to <i>context.stopService(Intent)</i>
- with a call to <i>service.stopSelf()</i>
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

<strong>Use IntentService for repetitive and/or long tasks that should run on the <i>background</i>, independent of the UI</strong>

### General:

- executes tasks sequentially
- runs in the background! No need to use Thread, HandlerThread...
- excellent for "Fire-and-Forget" tasks
- not possible to cancel tasks from the outside

### Lifecycle
Start:

- an IntentService starts with a call to <i>context.startService(Intent)</i>
- if the IntentService is already running, the new task is queued (FIFO)

End:

- the IntentService stops itself automatically when there are no more Intents to process (No need to call stopSelf()!)
- the system must recover memory for other (more important) apps 


### Example
<pre><code class="language-java">
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
</code></pre>

## AsyncTask

<strong>Use AsyncTask for short(!) (repetitive) tasks that are tightly <i>bound to the UI</i></strong>

### General

- allows to run code in the background and to synchronize again with the main thread
- excellent for short background operations which need to update the user interface
- a convenient way to perform long operations (a few seconds) from within an Activity
- computation runs on a background thread and whose result is published on the UI thread
- a task can be executed only once
- tasks are executed sequentially on a single thread! If you want parallel execution, you have to use <i>asyncTask.executeOnExecutor(THREAD_POOL_EXECUTOR, args)</i>
- does not handle configuration changes automatically (common solution: use a retained headless fragment)
- AsyncTask instance must be created on the UI thread.

### Lifecycle
<u>Start:</u>

- an AsyncTask starts with a call to <i>asyncTask.execute(Params, Progress, Result)</i>

<u>End:</u>

- when the AsyncTask finished work
- by calling <i>asyncTask.cancel(false)</i>. The implementation of <i>doInBackground(Params...)</i> has to check periodically the return value of <i>isCancelled()</i>.

### Example

<pre><code class="language-java">
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
</code></pre>

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

<u>Thread:</u>

- A Thread can have only one Looper. A Looper can have many unique Handlers associated with it
- A Thread gets a Looper and MessageQueue by calling <i>Looper.prepare()</i> after its running

<u>MessageQueue:</u>

- Unbounded linked list of messages to be processed on the consumer thread
- Holds the list of messages to be dispatched by a Looper
- A Looper has only one MessageQueue
- Messages are added via Handler objects associated with the Looper

<u>Message:</u>

- Container object carrying either a data item or a task (Runnable)
- Inserted by producer threads
- Consumed by consumer thread

<u>Looper:</u>

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

<pre><code class="language-java">
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
</code></pre>

<pre><code class="language-java">
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
</code></pre>

## HandlerThread

<strong>Use HandlerThread to establish an ongoing, one-way inter-thread communication</strong>

### General

- Just a "normal" Thread which sets up the internal message passing mechanism automatically → No need to init a Looper manually...
- Use HandlerThread instead of "normal" Java Thread with manual Looper setup (see code example above)
- Applicable to many background execution use cases, where sequential execution and control of the message queue is desired
- Use the constructor with the name argument → simplifies debugging
- Started in the same way as a Thread → <i>start()</i>
- Terminated either with <i>quit()</i> or <i>quitSafely()</i>
	- <i>quit()</i> - Terminates the Looper without processing any more messages in the MessageQueue
	- <i>quitSafely()</i> - Similar to the previous version but makes sure that the pending messages that are due to be delivered are handled

### Example

<pre><code class="language-java">
HandlerThread myThread = new HandlerThread("MyHandlerThread");
myThread.start();
Handler handler = new Handler(myThread.getLooper()) {
	@Override
	public void handleMessage(Message msg) {
		super.handleMessage(msg);
		// TODO: process message
	}
};
</code></pre>

## Loaders

### General

- framework to run asynchronous operations with content providers or other data sources
- loads data asynchronously
- callback when content changes or is added to the data source
- accessible via Activity or Fragment
- supports configuration change 

## Development Phase & Debugging

- StrictMode helps to identify long-running operations on the main thread
- Simulate how your app responds to being killed: <i>adb shell am force-stop com.example.packagename</i>
- Get process info: <i>adb shell ps | grep com.example.packagename</i>

Feedback? Questions? Errors? Let me know what you think.