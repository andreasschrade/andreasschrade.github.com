---
layout: post
title: "Java Rätsel #2: Wie genau sind Fließkommazahlen in Java?"
---




Diverse Excel-”Bugs” (primär Excel 97) indem Excel schlichtweg Probleme bei der Berechnung und Rundung von Fließkommazahlen hat sind weitgehend bekannt.

Wie sieht es allerdings mit der Programmiersprache Java aus? Gibt es bei Java ähnliche Rundungs- und Berechnungsprobleme im Zusammenhang mit Fließkommazahlen?

Der zweite Teil meiner Java Rätsel Serie möchte dieser Frage auf den Grund gehen und prüfen, wie sich die primitiven Datentypen zur Repräsentierung von Fließkommawerten hinsichtlich Genauigkeit schlagen.

Hiermit komme ich auch gleich zum Rätsel:
<strong>Was wird auf der Konsole ausgegeben?</strong>

{% highlight java %}
public class FloatingPrecisionPuzzle {
    public static void main(String[] args) {
        double zahl = 0D;
            zahl += 0.3;
            zahl += 5.1;
            System.out.println(zahl);
    }
}
{% endhighlight %}

<strong>Auflösung:</strong>

Vorrausgesetzt die Mathematik Fähigkeiten aus der 1. Klasse sind noch präsent, würde man höchstwahrscheinlich eine Ausgabe des Wertes “5.4” erwarten.
Das Rätsel wäre aber nunmal kein Rätsel, wenn genau dass der Fall wäre. :-)
Tatsächlich wird die scheinbar skurille Zahl <strong>5.3999999999999995</strong> auf der Konsole ausgegeben.
Aber worin liegt die Ursache?

<strong>Hintergrund:</strong>

Gleich vorweg, es handelt sich hierbei um kein Java spezifisches Problem.
Vielmehr ist das “Problem” auf die binäre Darstellung von Fließkommazahlen im Allgemeinen zurückzuführen.

Nehmen wir zur Veranschaulichung beispielsweise die Zahl 77.1, die wie folgt binär repräsentiert wird.

{% highlight java %}
0100 0000 0101 0011 0100 0110 0110 0110
0110 0110 0110 0110 0110 0110 0110 0110
{% endhighlight %}

Interessant bei betrachten der Repräsentation ist das sich wiederholende Muster von “0110″. Es verhält sich damit ähnlich wie die Dezimalrepräsentation von 1/3 (0,33333333333333…).
Was für 77.1 auf Binärebene gilt, gilt ebenso für 1/3 auf Dezimalebene. Beide Darstellungen können nicht exakt in ihrem jeweiligen System abgebildet werden.

Identisch verhält es ich auf die im Beispiel herangezogene Zahl. Die Zahl 0.1 lässt sich ebenso nicht exakt im Binärformat darstellen.
Es handelt sich hierbei ebenso wie 77.1 um eine sogenannte wiederholende Binärzahl..

Kann eine Fließkommazahl nicht eindeutig dargestellt werden, wird diese auf den nächsten darstellbaren Wert “gerundet”, was schließlich die Ungenauigkeit verursacht.

<strong>Alternativen:</strong>
Generell muss in allen Fällen, in denen eine exakte Genauigkeit erforderlich ist (Währung, …) entweder auf Ganzzahlen zurückgegriffen werden(
Währungen bzw. Geldbeträge werden als Centbeträge gehandhabt) oder aber es werden Datentypen herangezogen, die auf Festkommazahlenebene (keine Fließkommazahlen) operieren (z.B. die Klasse BigDecimal).

Die Klasse BigDecimal ist eine immutable Klasse, die es erlaubt, sowohl sehr große als auch sehr kleine Zahlenwerte korrekt darzustellen.
Folgendes Code-Beispiel entspricht einer exakten Nachbildung des vorangehenden Puzzle-Codes, mit dem Unterschied, dass das richtige Ergebnis ausgegeben wird.


Wichtig bei der Verwendung der BigDecimal-Klasse ist, dass nicht der Konstruktor der als Parameter einen “double”-Wert erwartet, verwendet wird.
Da hätte nämlich den Nebeneffekt das die Ungenauigkeit von double mit in das BigDecimal-Objekt “übergeben” wird.

Vielmehr sollte die Methode valueOf verwendet werden, die als einziges Argument ein Objekt vom Typ String erwartet.

Die Ausgabe des Wertes als String empfiehlt sich über die Method toPlainString vorzunehmen, da die toString-Methode ggf. mathematische, nicht aber rein dezimale Ergebnisse liefert.

<strong>Ausblick</strong>

Ich hoffe, ich konnte einen brauchbaren Überblick über die interne Handhabung von Fließkommazahlen in Java vermitteln und die Problematik, wieso die Primitives double/float nur annähernd korrekte Werte repräsentieren, aufzeigen.