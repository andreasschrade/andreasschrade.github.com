---
layout: post
title: "Java Rätsel #3: String Constant Pool"
---




Das mittlerweile dritte Rätsel beschäftigt sich im Kern mit der allgegenwärtigen, aber dennoch recht speziellen Klasse String.
Hiermit komme ich auch gleich ohne große Umschweife zum Rätsel:

<strong>Was wird auf der Konsole ausgegeben?</strong>

{% highlight java %}
public class StringConstantPool {
    public static void main(String[] args) {
        String a = "Beispiel";
        String b = "Beispiel";
 
        String c = new String ("Beispiel");
        String d = new String ("Beispiel");
 
        System.out.println(a == b);
        System.out.println(c == d); 
    }
}
{% endhighlight %}

<strong>Auflösung:</strong>
Ganz generell ist bekannt, dass nur primitive Datentypen in Java mit dem Gleichheits-Operator (“==”) verglichen werden dürfen bzw. nur hier auch wirklich der eigentliche Wert und nicht die Referenz verglichen wird.
Bei komplexen Datentypen wird unter Verwendung des Gleichheit-Operator lediglich die Referenz verglichen, weshalb ein derartiger Vergleich nicht das gewünschte Ergebnis liefert.
Der Vergleich komplexer Objekte ist, sofern die equals-Methode in der jeweiligen Klassen entsprechend überschrieben und implementiert ist, unter Verwendung der Methode equals möglich.

Doch, nun zurück zur eigentlichen Fragestellung.
Was wird auf der Konsole ausgegeben?
{% highlight java %}
true
false
{% endhighlight %}

<strong>Hintergrund:</strong>
Zuerst werden zwei Strings (“a”, “b”) mit der direkten (kurzen) Zuweisung erzeugt.
Anschließend werden weitere String Objekte (“b”, “d”) über den new-Operator erzeugt.
Alle 4 String-Objekte beinhalten die Zeichenkette “Beispiel” und sind somit zumindest auf rein logischer Ebene identisch.
Der Vergleich der über den new-Operator erzeugten String-Objekte gibt wie erwartet false zurück.
Das entspricht soweit vollständig dem Verhalten von komplexen Datentypen bzw. deren Referenzen in Java.

Wieso der Vergleich der beiden direkt zugewiesenen String-Objekte richtig ist bzw. funktioniert liegt schlichtweg an der Java Eigenheit bzw. der Funktionsweise des String Constant Pool.
Kurz gesagt besteht die Absicht des String Constant Pool eine effiziente Nutzung des Speichers.
Das soll dadurch ermöglicht werden, indem identische Strings nicht mehrfach gehalten werden, sondern lediglich die Referenz neu erzeugter String Objekte auf die bereits vorhandenen Objekte verweist.
Mit anderen Worten bedeutet es, dass die JVM bei der typischen Erzeugung von String prüft, ob dieser String bereits in dem String Constant Pool vorhanden ist und falls dies zutrifft lediglich die Referenz der neuen Variable auf das bereits bestehende String-Objekt zeigt.

Auf das Beispiel angewandt würde es bedeuten, dass bei der Erzeugung des ersten Objektes ein neues String-Objekt in dem Pool erzeugt wird und die Referenz (a) darauf verweist.
Bei der Erzeugung des zweiten Objektes erkennt die JVM, dass bereits das “identische” Objekt vorhanden ist, erzeugt somit kein weiteres Objekt im Pool, sondern lässt den Pointer (b) ebenso auf das bereits existierende Objekt zeigen.

Werden String-Objekte wie gewöhnliche andere Objekte über den new-Operator erzeugt, wird der Constant Pool umgangen und das Objekt wie gewohnt gehandhabt. Es wird ohne Prüfung des String Constant Pool erzeugt und erhält damit eine völlig neue Referenz.

<strong>Fazit:</strong>
Es gibt vermutlich kein wirkliches Anwendungsgebiet, indem die Erzeugung von String Objekten über den new-Operator unabdingbar ist.
Im Prinzip sollte immer die direkte Zuweisung von Strings verwendet werden um das Memory-Management hinsichtlich des Constant Pool Mechanismus ideal auszunutzen.