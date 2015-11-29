---
layout: post
title: "How to use the greenrobot EventBus library"
comments: true
language: "EN"

---

Greenrobot's <a href="https://github.com/greenrobot/EventBus">EventBus</a> is a tiny library that allows publish-subscribe style communication between components without requiring the components to explicitly register with one another. 

That makes the data exchange between components like Activity, Fragment, Services and any kind of backgrounds threads pretty easy.

<img style="display:block; margin: 0 auto" src="{{ site.url }}/assets/eventbus-greenrobot.jpg">

## Why should I care about Event Bus?

The short answer: loose coupling. 

The long answer: Normally, a software is divided into multiple layers like presentation layer (Activity, Fragment), business layer (e.g. UserService) and data layer (e.g. UserDao). Besides the technical layers, there are also functional layers.

Sometimes you want to process specific events that are interested for multiple parts of your application. Event Bus offers a solution for this case: It provides a centralized way to notify software components that are interested in a specific type of event. There exists no direct coupling between the code of the publisher of the event and the code that receives it. A well designed decoupled system will reduce the need to make changes across components in case anything changes. 

An Event Bus can also massively reduce boilerplate code: No need for interfaces, callbacks for asynchronous communication or data propagation through all software layers. It keeps your software architecture decoupled and flexible.


## Basics

The first step is to add the greenrobot EventBus dependency to your app's build.gradle file. Don't worry, the library is tiny (< 50 kB):

{% highlight xml %}
compile 'de.greenrobot:eventbus:2.4.0'
{% endhighlight %}

You need the following four parts to send and receive messages through your EventBus:

- The bus
- The event
- The sender
- The subscriber


### The bus

For most applications, you will only need to use one *EventBus* object throughout your app. There is no need for explicit object instantiation. Just call anywhere in your code *EventBus.getDefault()* to receive the „default” event bus instance:

{% highlight java %}
EventBus myEventBus = EventBus.getDefault();
{% endhighlight %}

### The event

Events are just POJO (Plain Old Java Object) without any specific implementation:

{% highlight java %}
public class HelloWorldEvent {
    private final String message;

    public HelloWorldEvent(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }
}
{% endhighlight %}

An event is just an object that is posted from the sender on the bus and will be delivered to any receiver class subscribing to the same event type. That's it!

### The sender

You can post any event from any part of your whole Android application. Just get the default *EventBus* instance by calling *EventBus.getDefault()* and submit any event by calling *post()*:

{% highlight java %}
EventBus.getDefault().post(new HelloWorldEvent("Hello EventBus!”);
{% endhighlight %}

### The subscriber

Ok, you have a bus, an event and already a sender. What's missing? Right, someone should listen to our *HelloWorldEvent*. You can declare as many subscribers as you want and you are not limited to specific Android classes. Theoretically, every Java class can act as a subscriber.


**Registration:**

You only have to call
{% highlight java %}
EventBus.getDefault().register(this); // this == your class instance
{% endhighlight %}
to listen and subscribe to events.

If you want to use an *Activity* or *Fragment* as a subscriber, just call the statement above inside *onStart()*, *onCreate()* or *onResume()*.


**Unregistration:**

You should also unregister the subscriber. Just call *unregister(this)* if you do not longer need the Event Bus:

{% highlight java %}
EventBus.getDefault().unregister(this);
{% endhighlight %}


**Listen to events:**

You have to declare a method inside your subscriber class with the name **onEvent** for every type of event you want to subscribe:

{% highlight java %}
// This method will be called when a HelloWorldEvent is posted
public void onEvent(HelloWorldEvent event){
  // your implementation
  Toast.makeText(getActivity(), event.getMessage(), Toast.LENGTH_SHORT).show();
}
{% endhighlight %}

No annotations and no configuration are necessary! You also don't have to implement a specific interface. Just plain old and easily understandable code. That is a prime example for the software design paradigm <a href="https://en.wikipedia.org/wiki/Convention_over_configuration" target="_blank">convention over configuration</a>.

The convention:

- method name: onEvent 
- parameter: only one method parameter 
- visibility: public
- return type: void

Of course, a subscriber can also listen to multiple events at the same time. The following snippet shows the necessary code to prepare a *Fragment* to listen to two different types of event:

{% highlight java %}
@Override
public void onResume() {
    super.onResume();
    EventBus.getDefault().register(this);
}

@Override
public void onPause() {
    EventBus.getDefault().unregister(this);
    super.onPause();
}

// This method will be called when a HelloWorldEvent is posted
public void onEvent(HelloWorldEvent event){
    Toast.makeText(getActivity(), event.getMessage(), Toast.LENGTH_SHORT).show();
}

// This method will be called when a JustAnotherEvent is posted
public void onEvent(JustAnotherEvent event){
    processEvent(event);
}
{% endhighlight %}

## Is there anything else interesting?

### Sticky Events

The idea behind sticky events is pretty similar to the concept of sticky Intents: <br\>
Sometimes your app subscribes to an event but does not want to wait until the next event appears. Instead, it is also okay to retrieve and process the latest submitted event. 

Consider the following case:

An app wants to show the current battery level in an *Activity*. The *Activity* does only receive the current battery info via an event when a change occurs (e.g. a drop from 43 % to 42 %). Let's say the user opens the *Activity* the first time and regular (non-sticky) events are used. <br \> What happens? The user must wait until a change of the battery level occurs to see the current battery level.

Sticky events solve this kind of problem. The Event Bus keeps the latest sticky event of a certain type in memory.

**How does it work?**

Sticky events are pretty similar to regular events. You only have to use *postSticky()* instead of *post()* and *registerSticky()* instead of *register()*:

{% highlight java %}
int batteryLevel = 42; // 42%
EventBus.getDefault().postSticky(new BatteryLevelEvent(batteryLevel));
{% endhighlight %}

Let's assume that this sticky event was posted few minutes ago.

What happens when the user wants to know the current battery level and enters the Activity right now?

During the registration of the sticky subscriber, the *Activity* will immediately get the latest posted *BatteryLevelChangeEvent*:

{% highlight java %}
@Override
public void onResume() {
    super.onResume();
    EventBus.getDefault().registerSticky(this);
}

@Override
public void onPause() {
    EventBus.getDefault().unregister(this);
    super.onPause();
}

public void onEvent(BatteryLevelEvent event) {
    myTextView.setText(event.getBatteryLevel() + " %");
}
{% endhighlight %}

By the way: It is also possible to remove the latest sticky event by calling:
*removeStickyEvent(myStickyEvent)*. You can also remove all sticky events by calling *removeAllStickyEvents()*.

### EventBus configuration

You can easily adjust the *EventBus* by using the *builder()* method:

{% highlight java %}
EventBus myEventBus = EventBus.builder(). … //your configuration methods
{% endhighlight %}

The following configuration methods exist:

- **logNoSubscriberMessages(boolean)**: <br> Enables/Disables the logging of the information that a posted event has no subscribers.
- **sendNoSubscriberEvent(boolean)**: <br> Enables/Disables the submission of the *NoSubscriberEvent* in case a posted event has no subscriber.
- **throwSubscriberException(boolean)**: <br> By enabling this feature, the application fails hard when a subscriber method throws an *Exception*.
- **sendSubscriberExceptionEvent(boolean)**: <br> By default, *EventBus* catches every exception thrown from *onEvent* methods and sends a *SubscriberExceptionEvent*. You can disable this feature by calling this method with „false“. 
- **skipMethodVerificationFor(Class)**: <br> By default, *EventBus* makes a method name verification for methods starting with *onEvent* to avoid typos. This method allows the exclusion of subscriber classes from this check.

For example, the following snippet creates an *EventBus* which does not send the event *NoSubscriberEvent* if there is no subscriber for a posted event:

{% highlight java %}
EventBus eventBus = EventBus.builder().sendNoSubscriberEvent(false).build();
{% endhighlight %}

## Conclusion

I hope you have learned how to use the EventBus library. Although the library is very tiny, it is so helpful to reduce boilerplate code and for creating a decoupled system.

Make sure you have a clear concept of your App. Other developers could otherwise be pretty confused about the magic.

Also, avoid nested events (Sender *A* sends Event X to Subscriber **B**. Subscriber/Sender **B** sends Event Y to Subscriber *C*). Nested events increase the complexity dramatically and make debugging very hard.

Every design pattern has its pitfalls. You only have to respect them.

<hr>

Thanks for reading. I will publish a new article about useful Android libraries every week. I'll tweet out every new article at <a href="https://twitter.com/andreasschrade">@andreasschrade</a>. Feel free to follow if you'd be interested in reading more.



