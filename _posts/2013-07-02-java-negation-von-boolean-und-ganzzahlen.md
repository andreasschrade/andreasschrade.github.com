---
layout: post
title: "Java: Negation von Booleans und Ganzzahlen"
---



## Hintergrund
Die Negation bzw. die Umkehrung findet sich gerade im Umgang mit Booleans recht häufig.
Die folgenden Beispiele verdeutlichen die Absicht. 

### Anwendung
Ein Kunde hat in einem Online-Shop die Möglichkeit, sich zum Newsletter an- bzw. abzumelden.
Der Zustandswechsel wird über die Methode toggleNewsletterSubscritption abgewickelt:

{% highlight java %}
class Customer{

private boolean isNewsletterSubscribed = false; 
 public void toggleNewsletterSubscribtion(){
	if(isNewsletterSubscribed) {
		// Wenn true
		isNewsletterSubscribed = false;
	} else {
		// Wenn false
		isNewsletterSubscribed = true;
	}
 }

}
{% endhighlight %}

Derartige Implementierung um Boolean-Zustandswechsel abzubilden finden sich durchaus häufig.
Aber es geht auch einfacher und eleganter mithilfe der Negation:

{% highlight java %}
public void toggleNewsletterSubscribtion () {
  isNewsletterSubscribed = !isNewsletterSubscribed;
}
{% endhighlight %}

Ist als Ausgangssituation keine Anmeldung vorgenommen, somit der Wert der Variable false, so wird die Expression (!isNewsletterSubscribed) auf true evaluiert. Andernfalls bei bestehender Anmeldung zu false.
Ersichtlich wird, dass der Negation-Operator, welches sonst hauptsächlich zur Verneinung innerhalb von If-Bedingungen seinen Einsatz findet, auch außerhalb im Zusammenhang mit booleschen Operationen seinen Einsatz finden kann.
Die Negation gehört dabei zu der Gruppe der logischen Operatoren und kann somit grundsätzlich auf boolesche Ausdrücke angewendet werden.


## Umkehrung von primtiven Zahlentypen

Neben der Umkehrung von dem primitiven Datentyp boolean (siehe vorheriges Kapitel) gibt es auch bei Zahlen eine einfache Möglichkeiten die entsprechende Kehrzahl auf eine ähnlich elegante Art und Weise, ohne jeglichen Einsatz statischer Hilfsmethoden, zu ermitteln. Anstelle eines logischen Operators wird hierzu der unäre arithmetische Minusoperator benötigt:

{% highlight java %}
int a = 42;
int b = -a; // -42
{% endhighlight %}

Eine Lösung, die auf dem ersten Weg möglicherweise ein wenig ungewöhnlich aussehen mag. Mithilfe des einfachen unären Minusoperator erfolgt die direkte Umkehrung eines primitiven numerischen Datentypen (int, double, …). Dies lässt sich dabei gleichermaßen auf Variablen und numerischen Konstante anwenden.

<strong>Hätten Sie gewusst,</strong>
dass sich der Kehrwert einer Zahl auch mit Hilfe des bitweisen „nicht“-Operators ermittelt werden kann?
{% highlight java %}
int a = 42;
a = ~a + 1;
{% endhighlight %}
Der bitweise Operator invertiert dabei grundsätzlich die gesetzten Bits seines Operanden:

Vorher:  00000000000000000000000000001010 =  42
Nachher: 11111111111111111111111111010101 = -43

Durch die zusätzliche Addition um 1 wird die Wertigkeit von 0 ausgeglichen und das Ergebnis von -43 auf -42 korrigiert. Dieses Vorgehen funktioniert allerdings nur für ganzzahlige Primitives und ist lediglich als ein Blick über den Tellerrand, zur Funktionsweise bitweiser Operatoren zu verstehen, und nicht als ein Best-Practice zur Umkehr von Zahlen. Zur Kehrwertermittlung gibt es schließlich, wie beschrieben, den unären Minusoperator.

