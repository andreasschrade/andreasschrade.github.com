---
layout: post
title: "Android: Datenaustausch zwischen Main-Thread und Worker-Threads"
---



## Hintergrund

Es gibt Situationen, in denen Operationen vom Main-Thread in separate Threads ausgelagert werden sollen.
Hierzu bietet Android grundsätzlich verschiedene Lösungsansätze. In diesem Beitrag soll es um eine schlichte und leichtgewichtige Implementierung auf Basis von <em>Handler</em> und <em>Looper</em> gehen.

### Implementierung

Zuerst geht es an die Implementierung des Worker-Threads: Hierzu erfolgt die Implementierung auf Basis eines einfachen Java-Threads:

{% highlight java %}
class MyThread extends Thread {
    @Override
    public void run(){
    }
}
{% endhighlight %}

Im Main-Thread der Applikation erfolgt somit der Aufruf via 

{% highlight java %}
MyThread mThread = new MyThread();
mThread.start();
{% endhighlight %}

Um nun Nachrichten zwischen Threads auszutauschen, gilt es dem empfangenden Thread eine MessageQueue zu geben.
Dies geschieht mithilfe der Klasse <em>Looper</em>. Die statische Methoden <em>prepare()</em> kümmert sich um die Erzeugung der Message-Queue.
Mit Aufruf von <em>loop()</em> wird die Bearbeitung der Message-Queue gestartet:

{% highlight java %}
class MyThread extends Thread {
    @Override
    public void run(){
           Looper.prepare();
           Looper.loop();
    }
}
{% endhighlight %}

<strong>Nachrichten senden und empfangen</strong>

Um Nachrichten empfangen und senden zu können wird eine Instanz vom Typ <em>Handler</em> benötigt. Wichtig an dieser Stelle ist, dass die erzeugte Handler-Instanz automatisch dem erzeugenden Thread zugeordnet wird.
Mit Implementierung der Methode <em>handleMessage(Message)</em> lässt sich ein individuelles Handling eintreffender Nachrichten realisieren.

{% highlight java %}
class MyThread extends Thread {
    public Handler mHandler;

    @Override
    public void run(){
           Looper.prepare();

           mHandler = new Handler() {
                   public void handleMessage(Message msg) {
                       // Nachricht verarbeiten
                   }
           };
           Looper.loop();
    }
}
{% endhighlight %}

Damit nun Nachrichten an diese Worker-Thread gesendet werden können, benötigt der Sender lediglich eine Referenz auf die Handler-Instanz des empfangenden Threads.
Mit Aufruf von <em>sendMessage(Message)</em> lässt sich anschließend eine Nachricht an den zugehörigen Worker-Thread versenden:

{% highlight java %}
Message msg = Message.obtain();
msg.obj = new HighComplexProcessingObject(someArgs);  // Eigentliche payload
mHandler.sendMessage(msg);
{% endhighlight %}