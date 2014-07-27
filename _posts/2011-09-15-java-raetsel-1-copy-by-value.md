---
layout: post
title: "Java Rätsel #1: Copy by Value"
---




Meine neue Serie “Java Rätsel” beschäftigt sich mit allerlei unterschiedlichen Themenbereichen der Java-Welt. Konkret lässt sich das eigene Java-Verständis anhand eines überschaubaren Code-Schnippels prüfen. Anschließend wird auf den technischen Hintergrund eingegangen…

Hiermit komme ich auch gleich zum ersten Rätsel, welches grundlegendes Verständnis zur Übergabe von Variablen an Methoden voraussetzt.

<strong>Frage: Was wird auf der Konsole ausgegeben?</strong>
{% highlight java %}
public class CopyByValueExample {
    public static void main(String[] args) {
        Food food = new Food();
        food.type = "Pizza";
 
        int a = 1;
 
        changeIt(food, a);
 
        System.out.println(food.type);
        System.out.println(a);
    }
 
    static void changeIt(Food food, int a){
        food.type = "Donut";
        a++;
    }
 
}
{% endhighlight %}

<strong>Auflösung:</strong>
Die Konsole gibt bei Ausführung des gelisteten Programmes folgende Werte aus:

{% highlight java %}
Donut

1
{% endhighlight %}

Aber warum wird lediglich das Food-Objekt verändert, nicht aber der Primitive?
Wie bereits der Titel des Blog-Artikels verrät, ist das Rätsels Lösung schlichtweg die Methodik namens CopyByValue.

Doch was bedeutet CopyByValue in diesem Zusammenhang?
Die Java Spezifikation sieht vor, dass das “Parameter-passing” in Java ausschließlich (wie in C) über die CopyByValue-Methodik geschieht.

Leider ist eine weit verbreitete Meinung, dass die Übergabe von komplexen Datentypen über das Verfahren CopyByReference geschieht.
Zum Verständnis warum dies schlichtweg falsch ist, möchte ich dies kurz erkären.

Pass-by-value bedeutet, dass die Zielmethode lediglich eine Kopie der ursprünglichen Variable erhält.
Auf Java bezogen bedeutet dies, dass bei primitiven Datenobjekte deren direkter Wert (auf das Beispiel bezogen wäre es “1″) kopiert bzw. in den lokalen Kontext der Methode dupliziert wird, da bei Primitives in Java keinerlei Referenzen/Pointer existieren.
Bei komplexen Datentypen existieren dagegen Referenzen. Die jeweiligen Referenzen werden beim Passing von komplexen Datentypen kopiert und zeigen damit auf den selben Speicherbereich des Zielobjektes.

Das hat zur Folge, dass wenn ein komplexer Datentyp an eine Methode übergeben wird, eine für die Methode gültige Methodenvariable angelegt wird, dessen Referenz auf den übergebenen Speicherbereich des Datentypes verweist.
Was würde mit dem ursprünglichen (“originalen”) Objekt passieren wenn die Methodenvariable ein neues Objekt zugewiesen bekommt?

{% highlight java %}
static void changeIt(Food food, int a){
    food = new Food();
    food.type = "Burger";
}
{% endhighlight %}

Es wird ein neues Objekt erzeugt, was in einem völlig neuen Speicherbereich abgelegt wird.
Die zuvor zugewiesene Referenz der “food”-Variable wird nun neu gesetzt; Die “alte”-Referenz auf die ursprüngliche (“Pizza”)-Referenz wird “verworfen”.
Folglich hat die Methode changeIt keinen Einfluss auf das in der main-Methode erstellte food-Objekt.
Die Ausgabe bleibt damit bei “Pizza” und ist nicht “Burger”.