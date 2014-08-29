---
layout: post
title: "Java: Hintergrund zur Performancesteigerungen im Umgang mit ArrayList"
---



## Optimierungsmöglichkeiten bei ArrayList
Das folgende Beispiel liefert eine gängige Vorgehensweise zur Befüllung einer Liste mit Elementen.
Exemplarisch wird die Liste mit Zufallszahlen im Integer-Bereich gefüllt. Dies soll uns nicht weiter stören, da das Beispiel lediglich auf intensive Befüllung der ArrayList mit zahlreichen Elementen abzielt.
Deshalb die Frage, wie lässt sich die zahlreiche Befüllung einer List bzw. einem Set grundlegend effizienter gestalten?

{% highlight java %}
Random rnd = new Random();
ArrayList<Integer> intList = new ArrayList<Integer>();
for(int i = 0; i < 4200; i++){
	intList.add(rnd.nextInt());
}
{% endhighlight %}

Im Umgang mit Listen gibt es zwei wichtige Kennzahlen zur Dimensionsangabe. Das bekannteste Attribute size, repräsentiert die Anzahl der tatsächlich vorhandenen bzw. zuvor hinzugefügten Elemente der Liste. Die Ermittlung aller Elemente erfolgt dabei über den Getter des Attribut size. Im Gegensatz dazu steht das eingeschränkt sichtbare Attribut capazity. Dieses Attribut beschreibt die Größe des darunterliegenden Array-Objektes. Eine ArrayList ist im Grunde nichts weiter als ein Array mit fester Größe in dem alle Elemente entsprechend typisiert gespeichert werden. Mit Initialisierung einer ArrayList wird der gekapselte Array mit einer Initialkapazität von 10 erstellt. Werden weitere Elemente hinzugefügt muss dieser Array bei erreichen der Kapazität entsprechend erweitert werden. Die Erweiterung erfolgt dabei mit Anlage eines neuen Arrays und anschließender Werteübernahme durch das Kopieren aller Werte. Zur Verbesserung der Performance hält die ArrayList-Implementierung einen Puffer vor, der bei jeder Erweiterung dem neuen Array eine neue Kapazität vergibt, die um 50 Prozent höher ist als die aktuelle Kapazität (load factor).

Ist zur Laufzeit der Applikation bekannt, dass eine bestimmte oder eine hohe Menge an Elementen eingefügt werden muss, sollte zugunsten der Performance die Kapazität der Liste vorab gesetzt werden.
Dies bewirkt, dass im Idealfall nur einmal der Array mit der benötigten Anzahl der Elemente erstellt werden muss und somit während der Iteration kein zyklischer Erweiterungsprozess stattfindet.
Die Initialkapazität kann dabei entweder über den Konstruktor festgelegt werden   (ArrayList(int initialCapacity)) oder nachträglich über die Methode ensureCapacity erfolgen:

{% highlight java %}
Random rnd = new Random();
ArrayList<Integer> intList = new ArrayList<Integer>();
intList.ensureCapacity(500);
for(int i = 0; i < 500; i++){
	intList.add(rnd.nextInt());
}
{% endhighlight %}

Steht bereits im vornherein die benötigte Gesamtkapazität fest, beispielsweise bei statischen Listen, so lässt sich bereits die notwendige Kapazität über den Konstruktor setzen.

{% highlight java %}
ArrayList<Integer> intList = new ArrayList<Integer>(500);
{% endhighlight %}

Als Gegenstück zur Methode ensureCapacity ermöglicht die Methode trimToSize den darunterliegenden Array auf die tatsächlich benötigte Kapazität zu reduzieren.

{% highlight java %}
Random rnd = new Random();
ArrayList<Integer> intList = new ArrayList<Integer>(500);
intList.ensureCapacity(500);
for(int i = 0; i < 500; i++){
	intList.add(rnd.nextInt());
}

intList.subList(10, 500).clear();
intList.trimToSize();
{% endhighlight %}

Der Aufruf der Methode macht Sinn, wenn die Menge der Einträge stark reduziert worden ist, und sicher ist, dass nicht unmittelbar die interne Kapazität des Arrays erneut beansprucht werden muss. Der Einsatz der Methode zielt somit primär auf die Verringerung des Speicherverbrauchs. Wirklich bemerkbar und lohnenswert ist der Aufruf der Methode jedoch erst bei einer Reduzierung der Array-Größe bei mehreren Millionen Elementen, da bei einem Array kein Overhead zur Speicherung der Elemente anfallen.

Im Detail lässt sich der Speicherverbrauch eines Array-Elements relativ einfach mit folgend abgebildeter Formel ermitteln:

Array-Objekt-Header + (Anzahl Elemente  * Speicherverbrauch je Element)

Wird die Formel auf einen Array angewendet, welches 1000 Elemente des primitiven Datentypen int aufnimmt, so lautet die Formel:

12 byte + (1000 Elemente * 4 byte) = 4012 byte

Die Rechnung lässt sich dabei fast auf alle primitiven Datentypen anwenden. Einzig der Primitive boolean benötigt mit 1 byte weit mehr als eigentlich zur Repräsentation benötigt wird (1 bit).
Enthält der Array keine Primitives sondern Referenzvariablen, so fallen für jedes Array-Element pauschal 4 Byte für die Referenz an (32bit VM). Dabei spielt es keine Rolle ob es sich um die null-Referenz, oder einer wirklichen Objekt-Referenz handelt. Bei Einsatz einer 64bit VM fällt anstelle der 4 byte mit 8 byte das Doppelte an.

