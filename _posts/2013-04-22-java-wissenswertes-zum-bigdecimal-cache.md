---
layout: post
title: "Java: Wissenswertes zum BigDecimal-Cache"
---



## Der BigDecimal Cache
Zur Vermeidung unnötiger Objekterzeugungen im gängien Wertebereich, bietet die Implementierung von BigDecimal einen einfachen Cache für sämtlichen Zahlen im gängigen Wertebereich. Die Funktionsweise ist dabei dem der Implementierungen für die Boxed-Typen der Primitives ähnlich.

Anstelle pauschal ein Objekt vom Typ BigDecimal über den Konstruktor zu erzeugen, empfiehlt es sich die Objektreferenz mittels der statischen Methode <em>valueOf</em> zu beziehen. Nur die Verwendung der besagten Methode bietet die Möglichkeit zur Nutzung eines Caches. Objekterzeugungen mittels dem Keyword new verursachen schließlich immer ein neues Objekt.

Während der Caching-Bereich von BigInteger sich auf -16 bis +16 beläuft, bietet der Cache von BigDecimal dagegen lediglich den Wertebereich von 0 bis 10 an. Die Objekt-Referenzen auf die gecacheden Objekte können neben der Methode valueOf auch direkt über die jeweiligen Konstanten bezogen werden (z. B. BigDecimal.ONE).

Verwirrend mag anfangs die Tatsache sein, dass valueOf als Methodentyp lediglich die beiden Primitives double oder long erwartet bzw. unterstützt. Es gibt daher keine überladene Methodenversion die ein Objekt vom Typ String erwartet. Dies ist insofern verwunderlich, da die Erzeugung eines BigDecimal-Objektes auf Basis eines double normalerweise keine gute Idee ist.

Die Entwickler der Klasse haben jedoch glücklicherweise diesen Fall bedacht. Anstelle den übergebenen double als Fließkommazahl zu verwerten, wird stattdessen die entsprechende String-Repräsentation der Zahl verwendet. Nach Konvertierung der Zahl in einen String, wird schließlich der „zuverlässige“ String-Konstruktor aufgerufen:

{% highlight java %}
new BigDecimal(Double.toString(val))
{% endhighlight %}

Durch diesen Kniff ist eine korrekte Ausgabe garantiert:

{% highlight java %}
BigDecimal b1 = new BigDecimal(1.1); // double-Konstruktor vermeiden
System.out.println(b1); // 1.100000000000000088817841970012523233890533447265625

BigDecimal b2 = BigDecimal.valueOf(1.1); // Best-Practice
System.out.println(b2); // 1.1
{% endhighlight %}
