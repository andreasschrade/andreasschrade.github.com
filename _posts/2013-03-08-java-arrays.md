---
layout: post
title: "Java: Einführung zu Arrays"
category: "java"
---



## Hintergrund
Der Array ist in Java ein spezieller Datentyp in der Verwaltung typgleicher Objekte die mithilfe eines ganzzahligen numerischen Index gesetzt und gelesen werden können.

### Anwendung
Während es für die Deklaration eines Arrays lediglich eine Möglichkeit gibt,

{% highlight java %}
int[] intArray;
{% endhighlight %}

gibt es dagegen zur Initialisierung mehrere Realisierungen.

Die erste Variante erfordert dabei den new-Operator. Im Anschluss an den Datentyp des Arrays steht die Gesamtkapazität des Arrays in eckigen Klammern:

{% highlight java %}
int[] intArray = new int[42];
{% endhighlight %}

Die zweite Variante erfordert dagegen keinen new-Operator, vielmehr werden in geschweiften Klammern die einzelnen Elemente kommasepariert aufgeführt. Der Initialisierungsprozess sowie die Zuweisung erfolgen daher in einem Schritt:

{% highlight java %}
int[] intArray = {1,2,3,5,8,13,21};
{% endhighlight %}

Die Initialisierung eines Arrays unter Verwendung der zuletzt genannten Shorthand-Variante hat im Vergleich zur erstgenannten keinerlei Unterschiede. Somit ergeben sich auch keine Vorteile in Hinblick auf die Performance bei  Verwendung der kompakten Variante.
Dies ist der Fall, da der Compiler die kurze Variante in die lange „unschöne“ Ausführung übersetzt. Somit entpuppt sich auch dieser Shorthand lediglich als syntaktischer Zucker, der auf Bytecodeebene keine Unterschiede birgt.

Folge Ausführungen sind dementsprechend äquivalent:

{% highlight java %}
String variantA[] = new String[]{„Apfel“, „Birne“};
String variantB[] = new String[2];
variantB[0] = „Apfel“;
variantB[1] = „Birne“;
{% endhighlight %}

Mit Initialisierung des Arrays erhält jedes Element des Arrays seinen initialen Wert. Primitive Datentypen erhalten ihren entsprechenden Standardwert (0, 0.0, false) sowie komplexe Datentypen null.
Das bedeutet, dass ein Array mit Primitives mit dem Initialisierungsprozess bereits seine maximale Speicherausprägung besitzt, während ein Array aus komplexen Datentypen erst entsprechend mit Objekt-Erzeugung via dem Keyword new den Speicher im Heap beansprucht.

Wenn möglich sollten Arrays ihrem Pendant der Collection aufgrund der höheren Effizienz vorgezogen werden. Ist die Gesamtkapazität des Arrays unklar, oder ändert sich häufig die Anzahl aller Elemente so empfiehlt es sich die Collection vorzuziehen.

