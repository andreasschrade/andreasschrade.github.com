---
layout: post
title: "Spring MVC – Asynchrone Verarbeitung mittels @Async-Annotation"
category: "java"
---



## Implementierung
Die @Async-Annotation stellt eine Möglichkeit unter Spring dar, den Aufruf einer Methode asynchron zu behandeln. Dies bedeutet, dass der Aufruf der annotierten Methode unmitellbar mit der weiteren Programmausführung fortfahren kann.

Der Aufruf der asynchronen Methode wird intern als ein Task gehandhabt, der in Spring's TaskExecutor zur Verarbeitung übergeben wurde.

Besitzt die annotierte Methode keine Rückgabe ist neben der Annotation keine weitere Implementierung notwendig:

{% highlight java %}
@Async
void doSomethingAsync(String myArgument) {
    // this will be executed asynchron
}
{% endhighlight %}

Die Annotation lässt sich aber auch auf Methoden anwenden, die einen Rückgabewert besitzen. In diesem Fall wird ein parametrisiertes Objekt vom Typ Future zurückgegeben:

{% highlight java %}
@Async
Future<String> doSomethingAsyncAndReturn(String myArgument) {
    // this will be executed asynchron
}
{% endhighlight %}

Mittels get() lässt sich anschließend gewohnt auf das Resultat zugreifen.