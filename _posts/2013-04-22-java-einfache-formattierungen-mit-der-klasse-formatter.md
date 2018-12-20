---
layout: post
title: "Java: Einfaches formatieren von Zeichenketten mittels der Klasse Formatter"
category: "java"
permalink: /java/2013/04/22/java-einfache-formattierungen-mit-der-klasse-formatter/
---



## Hintergrund
Erfolgt eine Verkettung aus festen Strings sowie dynamischen bzw. variablen Objekten, so ist schnell ein Punkt erreicht, an dem die Code-Wartbarkeit und Verständlichkeit leidet:

{% highlight java %}
private String formatInvoiceInfoBox(Order order) {
  String customerNr = order.getCustomerNr();
  String invoiceNr = order.getInvoiceNr();
  Date invoiceDate = order.getDate();
  SimpleDateFormat sdf = (SimpleDateFormat)
    DateFormat.getDateInstance(DateFormat.MEDIUM, Locale.GERMAN);

  String formattedDate = sdf.format(invoiceDate);

  return "Rechnungs-Nr: " + invoiceNr + " \nKunden-Nr: "
    + customerNr + "\nDatum: " + formattedDate;
}
{% endhighlight %}

Ausgabe:<br>
Rechnungs-Nr: A133742<br>
Kunden-Nr: 20042<br>
Datum: 26.01.2013

Wie sich eine derartige Zeichenformattierung eleganter lösen lässt, erfahren Sie im folgenden Abschnitt.


### Zeichenformattierungen mit <em>Formatter</em>
Anhand vieler einzelner konstanten Zeichenketten verschlechtert sich die Verständlichkeit. Um dem entgegenzuwirken eignet sich die statische Methode <em>format</em> der Klasse <em>String</em>.

Diese stellt ein einfaches und mächtiges Werkzeug zugleich dar. Über den variablen Methodenparameter können beliebig viele Argumente übergeben werden. Dies fördert die Lesbarkeit. Die eigentliche Zeichenkette bleibt bei diesem Ansatz nicht länger zusammenhangslos:

{% highlight java %}
private String formatInvoiceInfoBox(Order order) {
  String customerNr = order.getCustomerNr();
  String invoiceNr = order.getInvoiceNr();
  Date invoiceDate = order.getDate();

  return String.format("Rechnungs-Nr: %s %nKunden-Nr: %s %nDatum: %3$te.%3$tm.%3$tY", invoiceNr, customerNr, invoiceDate);
} 
{% endhighlight %}

Als erstes Argument der Methode format, wird die eigentlichene Zeichenkette erwartet. Diese kann mit Steuerzeichen bereichert werden, die anschließend mit Ausführung der Methode aufgelöst bzw. ersetzt werden. Das gängstige Steuerzeichen ist "%s", welches lediglich einen Platzhalter für ein String-Objekt darstellt. Dieser Platzhalter wird anschließend durch ein Objekt ersetzt, welches ausgehend vom zweiten Methodenparameter der Methode format übergeben wird. Wird dieses Steuerzeichen mehrmals eingesetzt, so erfolgt die Ersetzung des Steuerzeichen entsprechend der Reihenfolge, wie die Objekte der Methode übergeben werden:

{% highlight java %}
String f = String.format("eins, %s, drei, %s", "zwei”, "vier”);
System.out.println(f); // eins, zwei, drei, vier
{% endhighlight %}

Neben den Steuerzeichen "%s" zur Repräsentierung eines Strings, ermöglicht das Steuerzeichen "%n" einen Zeilenumbruch. Der Ausschnitt "%3$te.%3$tm.%3$tY”  im vorhergehenden Beispiel setzt sich dabei aus drei Bestandteilen zusammen.
Der erste Teil (%3$te) weist die Methode an, sich auf das dritte übergebene Argument zu beziehen (%3) und ausschließlich den Tag ($te) des Datums auszugeben. Ähnlich verhält es sich für das Monat  (.%3$tm), sowie für das Jahr (.%3$tY). Damit die Steuerzeichen zur Ausgabe des Datums funktionieren, muss entsprechend ein Objekt vom Typ Date an der besagten Position übergeben werden.

Zur variablen Übergabe der Argumente macht sich dabei die Methode <em>format</em> das in Java 1.5 hinzugekommene Sprachmittel der variablen Argumentenliste (varArg) zunutze.
Der Gedanke hinter dieser Funktion ist relativ einfach. Es besagt, dass keine feste Anzahl an Methodenparameter in der Methoden-Signatur festgelegt werden muss.
Wichtig bei der Verwendung von variablen Argumentlisten ist, dass alle Argumente den gleichen Typ besitzen und die Definition der varArg an das Ende der Methoden-Signatur gesetzt wird. Bei Missachtung der zuletzt genannten Regel, würden andernfalls Probleme bei der Zuordnung der Methodenparameter entstehen, da nicht ersichtlich wäre, wann die Aufzählung eines VarArg endet.

Innerhalb der Methode wird ein Vararg wie ein Array behandelt und kann deshalb recht schnell mittels der for-Schleife ausgewertet werden:

{% highlight java %}
public static void varArgTest() {
  sum(1, 2); // ein varArg-Parameter; Summe: 3
  sum(1, 2, 3, 4); // drei varArg-Parameter; Summe: 10
}

public static int sum(int initial, int... varArg) {
  for(int a : varArg)
  {
    Initial += a;
  }
  System.out.println("Summe: " + initial);
}
{% endhighlight %}

Neben der Methode <em>format</em> der Klasse <em>String</em> gibt es in der Klasse <em>PrintStream</em> eine weitere, in der Funktionsweise identische Methode. Beide stützen sich dabei auf die Klasse Formatter, welches die eigentliche Formatierung anhand von Steuerzeichen vornimmt. Eine Instanz der Klasse PrintStream verwendet als öffentliche Klassenvariable out, auch die Klasse System. Dies ermöglicht formatierte Konsolenausgaben:

{% highlight java %}
System.out.printf("Error: %s, Message: %s”, error, message);
{% endhighlight %}

Mit der Klasse Formatter befindet sich somit ein mächtiges Werkzeug zur Formatierung von Zeichenketten im Java-Reportoire. Von Grund aus wird eine Vielzahl unterschiedlicher Formatierungsmöglichkeiten unterstützt, die von einfachen Textverknüpfungen bis hin zu komplexen Datumsformattierungen reicht.
