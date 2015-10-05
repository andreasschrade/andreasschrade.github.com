---
layout: post
title: "Java: Entfernen von Items einer Collection während einer Iteration (best practice)"
category: "java"
---




Es gibt verschiedene Situationen in der das Entfernen von Listen-Einträgen während einer Iteration benötigt wird.
Klingt einfach! Wie wäre es mit folgendem Code-Beispiel?

{% highlight java %}
for (Object i : l) {
    if (condition) { 
         l.remove(i);
    }
}
{% endhighlight %}

Das Beispiel kompiliert und funktioniert bzw. kann problemlos ausgeführt werden.
Allerdings gibt es Situationen, in der das geschilderte Beispiel zu ernsthaften Problemen führen kann.

Falls die Collection während der Iteration verändert werden würde, würde dies mit einer Exception (ConcurrentModificationException) quittiert werden.
Aufgrund des inkonsistenten Zustandes der Collection bleibt ein gewisses Fehlerpotential bei dieser Shorthand Variante übrig.

Die einzig sichere und richtige Methode eine Java Collection während einer Iteration zu verändern ist der Weg über die Iterator Instanz.

{% highlight java %}
Iterator<String> iter = l.iterator();
while (iter.hasNext()) {
    if (iter.next().equals("delete")){
        iter.remove();
    }
}
{% endhighlight %}

Wesentlich ist hier die remove-Methode der Iterator Instanz, anstelle der Collection Instanz, zu verwenden.
Folgend ein völlig generischer Ansatz der sich für die Filterung von Collections anbietet:

{% highlight java %}
for (Iterator<?> it = l.iterator(); it.hasNext(); )
        if (!cond(it.next()))
            it.remove();
{% endhighlight %}

Iterator.remove ist letztlich die einzig sichere Methode eine Collection während deren Iteration zu manipulieren.
Nur so lässt sich der Zugriff auf den unberechenbaren Zustand sicher realisieren.