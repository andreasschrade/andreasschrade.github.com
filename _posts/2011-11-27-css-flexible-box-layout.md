---
layout: post
title: "CSS3 – Das Flexible Box Layout im Detail"
---




Bei der täglichen Layout-Erstellung von Webseiten ist in der Regel eine häufig anfallende Tätigkeit, die vertikale bzw. horizontale Anordnung von Elementen.
Für diesen Zweck bietet CSS3 nun (endlich!) einen Mechanismus, der sich diese Aufgabe in gewisser Weise speziell annimmt.

Flexbox:
Das sogennante “Flexible Box Layout” ist ein “neuartiges” Box-Model welches es erlaubt, Child-Elemente innerhalb einer “FlexBox” entweder horizontal oder vertikal auszurichten.
Ein freier bzw. nicht verwendeter Platz kann einem Child-Element zugeordnet werden, welches sich entsprechenden flexibel erweitert, bis die gesamte Fläche eingenommen ist.
Flexboxen lassen sich dabei problemlos verschachteln (Vertikale Flexbox innerhalb einer horizontalen Flexbox).
Diese neue CSS3-Funktion entpuppt sich zu wahren Werkzeug zur flexiblen Layout-Erstellung. Besonders in Zeiten von Responsive Design schont ein derartiges Mittel eine andernfalls exzessive Verschachtelungen von Div-Elementen.

<strong>Wesentliche Vorteile:</strong>

* Floatende Elemente, die gerade zur horizontalen, dynamischen Ausrichtung von Elementen verwendet werden “mussten”, gehören nun der Vergangenheit an. Mit diesem Modell lässt sich dieses Anwendungsgebiet vollständig übernehmen.
* infachere dynamischere Webseiten. Das Web wurde schließlich für fluide und nicht etwa für statische Webseiten mit einer festen Breite ausgelegt. Das FlexibleBox-Modell hilft dem Entwickler bei der Erstellung solcher Layouts und ist, was die Dynamisierung betrifft, ein würdiger Nachfolger der dafür meist verwendeter Eigenschaft “display-property :table”.

{% highlight java %}
true
false
{% endhighlight %}

<strong>Im Detail:</strong>
Mit dem Einzug des neuen Layout-Modells erhält die display-Eigenschaft folgende neue Attribute:

<table>
<tr>
<td>
box
</td><td>Versetzt das Element und dessen unmittelbaren Childs in das flexible Box Model</td>
</tr>
<tr>
<td>
box-orient (inline-axis (default) | horizontal | vertical | inherit)</td>
<td>Gibt die Ausrichtung der Child-Elemente an. Horizontale bzw. Vertikale Ausrichtung innerhalb des Parent</td>
</tr>
<tr>
<td>
box-pack (start | end | center | justify )</td>
<td>Setzt die Ausrichtung der Child entsprechend der box-orient angegebene Achse.
Wenn box-orient beispielsweise horizontal ist, wird dadurch die genaue horizontale Ausrichtung festgelegt</td>
</tr>
<tr>
<td>
box-align (start | end | center | baseline | stretch</td>
<td>Das entsprechende Gegenstück zu box-pack. Setzt die Ausrichtung der Childs entgegen der festgelegten Aurichtung.
Ist z.B. die Ausrichtung auf horizontal festgelegt, bestimmt dieser Wert dieser vertikale Ausrichtung der Childs.</td>
</tr>
<tr>
<td>
box-flex ( 0 (Default) | Ganzzahl)</td>
<td>Wert, den die Child Elemente festsetzen können.
Legt den Flexibilitätsgrad für einen einzelnes Child-Element fest.
Wobei der Standardwert “0″, für nicht flexible steht. Ein Wert von “2″ zu einem Nachbar-Child mit einem Wert “1″ beansprucht doppelt so viel Platz.</td>
</tr>
</table>


<strong>Beispiel:</strong>
Angenommen es gilt drei Div-Elemente horizontal nebeneinander auszurichten, wobei deren Höhe immer die gleiche sein soll.
Standardmäßig könnte man mit CSS 2.1-Mitteln, die DIV-Elemente floaten und das Elternelment dabei auf “overflow:hidden” setzen.

{% highlight html %}
<div id="box">
       <div>Element A</div>
       <div>Element B</div>
       <div>Element C</div>
  </div>
{% endhighlight %}

Mit Einsatz der Flexbox geht dies deutlich eleganter und flexibler:

{% highlight css %}
#box{  
    -moz-box-orient: horizontal;
    -webkit-box-orient: horizontal;
    box-orient: horizontal;
 
    display: -moz-box;
    display: -webkit-box;
    display: box;
}
{% endhighlight %}

Dem Parent wird dabei angewiesen, seine direkten Child-Elemente horizontal auszurichten und dabei selbst in den FlexBox-Model zu wechseln.
Kleiner Schönheitsfehler ist hierbei leider noch die mangelnde direkte Unterstützung der Browser.
Aktuell ist hierzu sowohl für Firefox als auch für Chrome die individuelle browserspezifische Eigenschaft notwendig.

<strong>Flexibilität:   </strong>
Den Namen wird das Box-Model durch das setzen des optionalen Flex-Parameters gerecht.
Dieser ermöglicht die anteilige Zuweisung des insgesamt verfügbaren Platzes an einem Child-Element.
Angenommen das erste Div soll immer die doppelte Breite besitzen wie die restlichen Child-Elementen.

{% highlight css %}
#box > div{
     -webkit-box-flex: 1;
  -moz-box-flex:1;
  box-flex:1;
}
#box > div:first-child{
  -webkit-box-flex: 2;
  -moz-box-flex:2;
  box-flex:2;
}
{% endhighlight %}

Das erste Div Element erhält hierbei den doppelten Faktor wie bei den restlichen Elementen. Auf diese weise lässt sich sehr einfach, dynamische und mehrspaltige Layouts erstellen.

<strong>Browser Unterstützung:</strong>
Keine wirkliche Neuigkeit und auch nicht der Rede wert ist die fehlende Unterstützung des FlexBox-Models im Internet Explorer.
WebKit Browser sowie der Firefox unterstützen diese Eigenschaft indirekt über die browserspezifischen Eigenschaften.