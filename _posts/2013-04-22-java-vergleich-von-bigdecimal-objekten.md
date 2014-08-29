---
layout: post
title: "Java: Vergleich von BigDecimal-Objekten"
---



## Korrekter Vergleich von BigDecimal-Objekten
Als komplexer Datentyp scheidet der Wertevergleich bei Objekten vom Typ BigDecimal mittels des Vergleichsoperators entschieden aus. 

Intuitiv würde stattdessen die Wahl auf die sonst übliche Vergleichsmethode <em>equals</em> fallen:

{% highlight java %}
BigDecimal a = new BigDecimal("42.12");
BigDecimal b = new BigDecimal("42.12");
System.out.println(a.equals(b)); // true
{% endhighlight %}

Das klappt, dennoch ist bei dem Wertevergleich aus dem Beispiel Vorsicht geboten. Üblicherweise vergleicht die <em>equals</em>-Methode beide Objekte auf deren vollständige Gleichheit. Dies impliziert bei der Klasse BigDecimal somit auch sämtliche Nachkommastellen.

Durch diesen Nebeneffekt können sich Situationen ergeben, indem der Vergleich mittels <em>equals</em> nicht das gewünschte Ergebnis liefert. Zwar sind nach unserem mathematischen Verständnis beide Zahlen im folgenden Listing gleicher Wertigkeit, intern gibt es jedoch Unterschiede bei der Abbildung beider Werte.

{% highlight java %}
BigDecimal a = new BigDecimal("1");
BigDecimal b = new BigDecimal("1.0");
System.out.println(a.equals(b)); // false
{% endhighlight %}

Mit dieser Besonderheit fällt die Klasse BigDecimal aus dem üblichen Schema, weshalb anstelle von <em>equals</em> die Methode <em>compareTo</em> zum Wertevergleich herangezogen werden sollte. Normalerweise sollte der Vergleich zwei identischer Objekte im Bezug auf die Implementierung von <em>equals</em> und <em>compareTo</em> identisch sein. Wird ein Vergleich mittels <em>equals</em> auf true evaluiert, so müsste entsprechend der Aufruf von <em>compareTo</em> den Wert 0 liefern. Der Rückgabewert von 0 bedeutet in diesem Fall, dass beide zu vergleichenden Objekte in ihrer Wertigkeit gleichbedeutet sind.

Zur Vermeidung des Fehlerpotentials in der Implementierung von <em>equals</em>, empfiehlt es sich, bei mathematischen Wertevergleichen die Methode <em>compareTo</em> anstelle von <em>equals</em> heranzuziehen:

{% highlight java %}	
BigDecimal a = new BigDecimal("1");
BigDecimal b = new BigDecimal("1.0");
System.out.println(a.compareTo(b) == 0); // true
{% endhighlight %}

Die Klasse BigDecimal sollte immer seinen Einsatz anstelle von double und float finden, wenn eine exakte Repräsentation von Fließkommazahlen benötigt wird. Andernfalls, wenn ein gewisser Grad an Ungenauigkeit verträglich ist, sollte aufgrund der höheren Performance und Komfortabilität der Primitive double herangezogen werden. 
Die Komfortabilität macht sich bei Verwendung komplexerer Formeln bemerkbar, da schließlich sämtliche mathematische Operationen über Methoden anstelle von Operatoren abgewickelt werden müssen.
Ein typisches Einsatzgebiet von BigDecimal ist die exakte Darstellung von Geldbeträgen. Leider wird jedoch stattdessen häufig zur Darstellung von Geldbetragen auf den Primitive double zurückgegriffen. Davon ist allerdings entschieden abzuraten! Geldbeträge sollten immer mithilfe eines BigDecimal-Objektes bzw. einer in der funktionsweise ähnlichem Datentypen abgebildet werden. Es gibt keinen Grund für Geldbeträge einen double zu verwenden!  

Ist eine besonders hohe Performance von Bedeutung, wäre auch als Alternative eine individuelle Währungsklasse auf Basis eines ganzzahligen Primitives wie long denkbar.
Der ganzzahlige Datentyp long könnte dabei den Geldbetrag in Cent repräsentieren und erst zur Darstellung das Komma mit einer Division durch 100 setzen.
