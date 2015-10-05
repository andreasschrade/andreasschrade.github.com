---
layout: post
title: "Java: Die null-Referenz (Teil II)"
category: "java"
---



## Hintergrund
Dies ist der zweite Artikel zur null-Referenz in Java. Der erste Artikel gibt neben einen grundsätzlichen Einblick zur null-Referenz zudem auch einen kurzen Abriss zu Optimierungen in Verbindung mit Short-Circuit-Operatoren und dem Null-Object-Pattern.


### Dokumentation im Umgang mit null
Zur weiteren Reduzierung von null-Prüfungen ist es immer eine gute Idee, wachsam die Dokumentation der zu verwendeten Bibiliotheken zu studieren und zu prüfen, ob die eingesetzte Methode null zurückgegeben kann oder nicht.
Empfehlenswert bei Einsatz des Javadoc ist die Angabe eines Hinweises, der Aufschluss gibt, ob null ein möglicher Rückgabewert ist:

{% highlight java %}
/**
  * Liefert die zuletzt vom Kunden getätigte Bestellung
  *
  * @return die zuletzt erfasste Bestellung. Im Fall, dass noch keine Bestellung erfasst wurde wird NULL zurückgegeben.
  */
 public Order getLatestOrder() {
 ...
 }
{% endhighlight %}
 
Der Einsatz von derartigen Hinweisen zur Verarbeitung von null sollte wenn möglich immer im eigenen Programmcode verwendet werden. Idealerweise sollte in der Dokumentation zu jeder Methode hinterlegt werden, ob sowohl null als Wert eines Methodenparamter zulässig ist und verarbeitet werden kann, sowie ob null ein möglicher Rückgabewert der Methode darstellt. 
Dies erlaubt es vor allem dem Entwickler unnötige null-Prüfungen im Programmcode zu vermeiden, wenn dieser sicherstellen kann, dass die eingesetzte Methode niemals null liefern kann.

## null im Umgang mit Arrays und Collections
Folgendes Beispiel zeigt ein Szenario, wo bislang null als möglicher Rückgabewert besteht.
Die Methode getOrderStatistic beabsichtigt die Ermittlung des Gesamtbetrags aller Aufträge eines beliebigen Datum. Dazu wird die Methode getOrdersByDate aufgerufen, die unter Umständen null zurückliefern kann. Eine Prüfung auf null ist somit bei der weiteren Verarbeitung des Rückgabewertes notwendig.

Wie würden Sie diese notwendige Überprüfung überflüssig machen?

{% highlight java %}
private BigDecimal getOrderStatistic (Date date) {
 BigDecimal totalCosts = BigDecimal.ZERO;
 List <Order> orderList = getOrdersByDate(date);
 if( orderList != null){
  for(Order order: orderList){
   totalCosts = totalCosts.add(order.getTotal());
  }
 }
return totalCosts;
}

private List<Order> getOrdersByDate(Date date) {
 if(date != null){
  return getFilteredOrderList(„orderDate“, date);
 }else{
  return null;
 }
}
{% endhighlight %}

Der Rückgabewert der Methode getOrdersByDate ist eine parametrisierte Liste vom Typ Order. Anstelle der einfachen Rückgabe von null empfiehlt es sich eine leere Liste zurückgegeben. Eine leere Liste stellt grundsätzlich keine Fehlinformation dar. Im Gegenteil, die Auswertung einer Liste erfolgt meist innerhalb einer Schleife sowie einem direkten Zugriff über die Methode get. In beiden Fällen würde es bei der Rückgabe von null zu einer Null-Pointer-Exception kommen. Wird dagegen ein leeres Listen-Objekt ausgeliefert, so führt dies zu keinerlei Problemen. Die Schleife erfährt keinerlei Iteration und die Ermittlung eines Elements via der Methode get führt genauso wenig zu einer Exception:

{% highlight java %}
private BigDecimal getTotalUmsatzByCustomer (Date date) {
  BigDecimal totalCosts = BigDecimal.ZERO;
  for(Order order: getOrdersByDate(date)){
   totalCosts = totalCosts.add(order.getTotal());
  }
return totalCosts;
}

private List<Customer> getOrdersByDate(Date date) {
 List<Customer> orderList = Collections.<Customer>emptyList();
 if(date != null){
  orderList = getFilteredOrderList(„orderDate“, date);
 }
  return orderList;
}
{% endhighlight %}

Immer wenn eine Methode eine Collection oder ein Array als Rückgabewert liefert, sollte sichergestellt sein, dass anstelle von null eine leere Collection bzw. ein leeres Array-Objekt zurückgegeben wird.
Das erspart auf Dauer unnötige Abfragen auf das Literal und reduziert dementsprechend die Fehleranfälligkeit.

Zur Vermeidung einer Objekt-Erzeugung, wenn immer eine leere Liste benötigt wird, sollte die statische Methode emptyList der Klasse Collections verwendet werden. Neben der Methode kann ebenso der Zugriff auf das leere Listen-Objekt mittels dem öffentlichen Attribut EMPTY\_LIST der Klasse Collections erfolgen. 
Neben dem Attribut EMPTY\_LIST gibt es auch die entsprechenden Pendants für ein Set-Objekt (Collections.EMPTY\_SET)sowie einer Map (Collections.EMPTY\_MAP).
Es spielt dabei keine Rolle, ob die Ermittlung des „leeren“ Objekt über das öffentliche Attribut oder der Methode erfolgt. Die jeweiligen Methoden liefern lediglich die Referenz des öffentlichen Attribut.

Für Array-Objekte verhält es sich identisch. Auch hierfür sollte anstelle von null ein leerer Array verwendet werden. Dies geschieht am einfachsten, indem für jeden Typ ein leerer Array zur Verfügung gestellt wird:

{% highlight java %}
private static final String[] EMPTY_STRING_ARRAY = new String[0];
{% endhighlight %}

Sowie alternativ, die gekürzte Fassung:

{% highlight java %}
private static final String[] EMPTY_STRING_ARRAY = {};
{% endhighlight %}

Es reicht dabei eine Array-Instanz für den mehrfachen Einsatz als null-Ersatz, da sich die Größe eines Arrays nachträglich im Programmablauf nicht ändern lässt und somit keinerlei Modifikation an diesem null-Objekt vorgenommen werden können. Somit wäre auch bei Arrays eine jeweilige Neuerzeugung überflüssig.

## null im Umgang mit Zeichenketten
Zur Repräsentation eines Objektes als String ist in der Oberklasse Objects die Methode toString vorgesehen.
Der Aufruf dieser Methode kann dabei sowohl explizit als auch implizit erfolgen:

{% highlight java %}
Order order = null;
            
// Impliziter Aufruf
System.out.println(order); // Ausgabe: „null“

// Expliziter Aufruf
System.out.println(order.toString()); // Null-Pointer-Exception
{% endhighlight %}

Während der implizite Aufruf null-freundlich ist, verhält es sich bei der expliziten Variante, wie erwartet, gegenteilig. Auf der Konsole wird somit nichts ausgegeben, da das Programm sich mit einer Null-Pointer-Exception beendet.
Wirklich null-freundlich lässt sich der explizite Aufruf unter Verwendung der Methode valueOf der Klasse String gestalten:

{% highlight java %}
System.out.println(String.valueOf(order)); // „null“
{% endhighlight %}

Für den Fall, dass das Argument null ist, wird der String „null“ zurückgegeben, ansonsten die entsprechende Zeichenkette.
Wichtig bei Verwendung dieser Methode ist, dass die richtige überladene Version verwendet wird. Zu dieser Methode existieren nämlich zwei überladene Versionen:

{% highlight java %}
String.valueOf(Object)
String.valueOf(char[])
{% endhighlight %}

Hierbei sollte darauf geachtet werden, nicht die Version zu verwenden, die als Methodenparameter einen Array vom Typ char erwartet. Diese Version ist nämlich nicht null-freundlich und verursacht eine Null-Pointer-Exception, wenn null übergeben wird.

Folgendes Beispiel veranschaulicht die Problematik. Was passiert, welche Ausprägung der Methoden-Signatur trifft zu?

{% highlight java %}
String result = String.valueOf(null);
{% endhighlight %}

Tatsächlich greift die überladene Version, die einen Array vom Typ char erwartet. Aber wieso ist das so?
Die Ursache liegt in der Art und Weise, wie die VM die zutreffende, überladene Methode auswählt. In der Java-Spezifikation findet sich hierzu ein komplexes Regelwerk, welches sich aber im Prinzip auf zwei wesentliche Grundsätze beschränken lässt. Grundsätzlich muss die Methoden-Signatur kompatibel sein. Dies reicht in den meisten Fälle zur Evaluierung der jeweiligen passenden Methodenversion:

{% highlight java %}
public void add(int a) { }
public void add(long b) { }
{% endhighlight %}

Erfolgt ein Aufruf der Methode mit

{% highlight java %}
add(42);
{% endhighlight %}

So wird die erste Ausprägung, die einen int als Methodenparameter erwartet verwendet, da automatisch ganzzahlige Konstanten einen int repräsentieren. In diesem Fall ist die Kompatibilität eindeutig.
Reicht diese Selektion nicht aus, bzw. stehen mehrere kompatible Methodenüberladungen zur Verfügung, so wird die meist spezifische Version verwendet:

{% highlight java %}
public void add(User user){}
public void add(Customer customer) {}

add(new Customer());
{% endhighlight %}

Da die Klasse Customer von dem Datentyp User erbt, würden nach der ersten Evaluierung beide Methoden infrage kommen, da prinzipiell beide Überladungen mit dem Aufruf kompatibel sind. Schließlich ist ein Objekt vom Typ Customer mit dem Datentyp User kompatibel. Für derartige Fälle greift schließlich der zweite Grundsatz. Dieser sagt aus, dass die die Methode ausgewählt wird, die meist spezifisch ist.

Konkret auf das vorherige Beispiel bezogen bedeutet dies, dass ein Array vom Typ char kompatibel mit dem Typ Object ist.
Umgekehrt gilt dieser Grundsatz nicht. Ein Objekt vom Typ Object ist nicht kompatibel mit einem Array vom Typ char.
Wenn man sich nun den im eingangs des Kapitels erwähnten Grundsatz verdeutlicht, dass null eine Art Untertyp eines jeden komplexen Datentypen in Java repräsentiert, so wird ersichtlich, weshalb nicht die Methoden-Variante die ein Object erwartet, verwendet wird.
Die char-Array-Signatur ist zu null kompatibel und spezifischer als ein Object.
Dies ist der Grund, weshalb die Null-Pointer-Exception geworfen wird und die Überladung verwendet wird, die einen Array vom Typ char erwartet.

Umgehen lässt sich dieses Problem durch einen expliziten Cast auf Object. Das erzwingt die Verwendung der „richtigen“ Überladung:

{% highlight java %}
String result = String.valueOf((Object)null);
{% endhighlight %}

Derartige Konstrukte, in denen das null-Literal auf einen konkreten Datentyp gecastet wird, erfolgen meist bei überladenen Methoden, die mit null als Methodenparameter aufgerufen werden. Ohne einen expliziten Cast würde sich die problematische Konstellation wie im vorherigen Beispiel ergeben, da möglicherweise eine falsche Methodenausprägung unbeabsichtigt verwendet wird:

{% highlight java %}
private void print (Document doc, PrinterSetting ps){
}

private void print (Document doc, ColorSchemata cs) {
}
{% endhighlight %}

Ohne einen expliziten Cast von null wäre die zu verwendende Methode bei Aufruf von 

{% highlight java %}
print(new Document(„123“), (ColorSchemata)null);
{% endhighlight %}

unklar. Der Cast von null schafft die notwendige Klarheit.

## Kleine null-"Kniffe" mit großer Wirkung

Auch im Bezug auf die null-Sicherheit gibt es diverse kleine Kniffe mit dennoch großer Wirkung.

Die im folgenden Listing dargestellte Methode liefert als Rückgabewert true, wenn die Bestellung vom Käufer bezahlt worden ist. Dies ist der Fall, wenn der Auftragsstatus (getOrderState()) den Statuscode „PAID“ besitzt. Im Bezug auf null-Sicherheit stellt in der Implementierung lediglich der Methodenparameter order ein Problem dar. Deshalb, wie lässt sich die Methode null-freundlich optmieren, ohne eine explizite Prüfung auf null vorzunehmen?

{% highlight java %}
private boolean isOrderPaid(Order order){
 return order.getOrderState().equals(„PAID“);
}
{% endhighlight %}

Allein durch Umstellung der Bedingung, wird die Methode vollkommen null-sicher:

{% highlight java %}
private boolean isOrderPaid(Order order){
 return („PAID“.equals(order.getOrderState())){
}
{% endhighlight %}

Der Ansatz hat den Hintergrund, dass mit Aufruf der Methode equals des  konstanten Wertes „PAID“ keinerlei Gefahr im Bezug auf eine Null-Pointer-Exception ausgeht, da eine Implementierung der Methode equals immer eine Prüfung auf null besitzen sollte! Dies macht es gegenüber der gebräuchlicheren Version, bei der die Konstante der equals-Methode übergeben wird, aus zweierlei Aspekten interessant.

Als wesentlicher Vorteil ergibt sich die angesprochene Sicherheit, da die Abfrage-Expression null-sicher ist und selbst bei Übergabe von null lediglich false zurückgibt.
Auch im Sinne der Code-Lesbarkeit ist das Vorgehen, die Konstante auf der linken Seite der Bedingung zu halten, vom Vorteil. Für gewöhnlich erfolgt die Wahrnehmung jeglichen Textes von links nach rechts. Durch die Umstellung steht der abzufragende und entscheidende Wert gleich am Anfang der Bedingung.

Dies macht sich vor allem bei einer Reihe an Vergleichen über ein längeres If-Statement bemerkbar:
 
 {% highlight java %}
if („PAID“.equals(order.getOrderState())){
   // …
}else if („NOT_PAID“.equals(order.getOrderState())){
   // …
}else if („ERROR“.equals(order.getOrderState())){
   // …
}
{% endhighlight %}

Im Vergleich zu:

{% highlight java %}
if (order.getOrderState().equals(“PAID”)){
   // …
}else if order.getOrderState().equals(“NOT_PAID”)){
   // …
}else if (order.getOrderState().equals(“ERROR”)
   // …
}
{% endhighlight %}

Dieses Vorgehen lässt sich auch auf Aufzählungstypen (Enumerations) anwenden:

{% highlight java %}
object = null;

boolean equals = object.equals(Enum.enumElement); // Null-Pointer-Exception
boolean equals = Enum.enumElement.equals(object); // false
{% endhighlight %}

Damit die Regel gilt, dass ein Vergleich über die equals-Methode durch Aufruf des konstanten Wertes keine Null-Pointer-Exception auslöst, gilt es in eigenen Implementierungen von equals darauf zu achten, dass auch eine Prüfung auf null stattfindet.

Obligatorisch erfolgt in der equals-Implementierung meist zuerst die Prüfung auf null der übergebenen und abzugleichenden Variable. Handelt es sich anschließend um ein Objekt des gleichen Typ, so erfolgt der eigentliche Wertevergleich.
Betrachten Sie bitte hierzu folgende Implementierung. Welche Abfrage ist darin überflüssig?

{% highlight java %}
@Override
 public boolean equals(Object anotherObject) {
    	if (anotherObject == null) {
        return false; // null-Sicherheit
      }      
      if (this == anotherObject) {
    	    return true; // identische Referenz
    	}

// Typkompatibilität
    	if (anotherObject instanceof Customer) {
    	    Customer anotherCustomer = (Customer)anotherObject;
          String anotherFirstname = anotherCustomer.getFirstname();
          String anotherLastname = anotherCustomer.getLastname();
          if(anotherFirstname.equals(firstname) 
            &&(anotherLastname.equals(lastname)){
            return true;  
          }
    	}
    	return false;
    }
{% endhighlight %}

Auf die Prüfung von null lässt es sich, wie wir im vorhergehenden Beispiel gelernt haben, nicht verzichten.
Für den Cast und dem eigentlichen Wertevergleich führt zudem kein Weg an der Prüfung der Typkompatibilität vorbei. Zusammenführen lässt sich jedoch die Prüfung auf null, sowie der Typkompatibilität nur durch Einsatz des instanceof-Operator. Die Prüfung, ob es sich bei der Instanz um ein Objekt des Typs Customer handelt impliziert ebenso eine Prüfung auf null.

Somit kann grundsätzlich auf eine explizite Prüfung auf null verzichtet werden, wenn zusätzlich die Typkompatibilität mittels instanceof geprüft wird.

{% highlight java %}
@Override
 public boolean equals(Object anotherObject) { 
      if (this == anotherObject) {
    	    return true;
    	}
    	if (anotherObject instanceof String) {
    	    Person anotherCustomer = (Customer)anotherObject;
          String anotherFirstname = anotherCustomer.getFirstname();
          String anotherLastname = anotherCustomer.getLastname();
          if(anotherFirstname.equals(firstname) 
            &&(anotherLastname.equals(lastname)){
            return true;  
          }
    	}
    	return false;
    }
{% endhighlight %}

## Fehlerhandling im Falle von null
Meistens kann mit Feststellung einer null-Referenz weniger sinnvoll reagiert werden, als wie es bei einer equals-Implementierung, mit Rückgabe von false, möglich ist.
Für Situationen, in denen innerhalb der Programmausführung keine Möglichkeit besteht auf die null-Referenz zu reagieren oder allein das Auftreten bereits ein klarer Fehlerfall darstellt, so muss sprechend darauf reagiert werden.
Obwohl in Methoden die Rückgabe von null eine gängige Praxis im Fehlerfall darstellt, so sollte in Anbetracht auf die bereits angesprochene Problematik möglichst darauf verzichtet werden.

Betrachten Sie bitte hierzu folgendes Beispiel:

{% highlight java %}
private Customer getCustomerById (String id){
  if(id != null) {
	return getCustomerObjectByAttribute(„ID“,id);
  }

  return null;
}
{% endhighlight %}

Allgemein gesprochen erwartet die Methode ein Objekt, welches als Kennzeicher zur Ermittlung eines Datensatzes dient. Es gibt keinen logischen Grund, weshalb die Übergabe von null als Kennzeichner einen legitimen Zustand darstellt. Schließlich ist vollkommen unklar, welches Objekt es anhand von null zu ermitteln gilt. Derartige klare Fehlerfälle werden jedoch meist lediglich mit der Rückgabe von null „gelöst“. Wohlgleich es im Programmablauf keinen Fall geben darf, in der die Methode mit null aufgerufen wird.
Im aktuellen Implementierungsstand würde dieser Fehler vorerst unbemerkt durch Rückgabe von null verschleiert werden, was bei Unachtsamkeit zur bekannten Exception im weiteren Programmablauf führen könnte.

Ratsam mit Feststellung, dass eine essentiell notwendige Variable nicht initalisiert ist oder einen ungültigen Wert repräsentiert und somit ein Fehlerfall darstellt, ist es auf den Fehler mithilfe einer Exception zu reagieren:

{% highlight java %}
private Customer getCustomerById (String id){
  if(id == null) {
    throw new IllegalArgumentExceptioon (“Id has not to be null“);
  }
  return getCustomerObjectByAttribute(„ID“,id);
}
{% endhighlight %}

Eingangs der Methode wird auf die notwendige Voraussetzung zur weiteren Ausführung der Programmlogik geprüft. Entspricht der Parameter nicht dem entsprechenden Format (z.B. außerhalb des notwendigen Wertebereichs) oder ist dieser null, so wird mit einer IllegalArgumentException auf den Fehlergrund hinreichend reagiert.
An dieser Stelle möge man sich nun vielleicht die Frage stellen, wieso eine IllegalArgumentException und nicht eine NullPointerException, die ja soweit zutreffender und spezifischer wäre, verwendet und geworfen wird?
Licht in das Dunkle bringt das JavaDoc der Null-Pointer-Exception, welches den Verwendungszweck der Exception wie folgt beschreibt:

„Notice that all of them are thrown by the runtime when null is used inappropriately“

Dem Gegenüber steht das beschriebene Einsatzgebiet der IllegalArgumentExcpetion:

„Thrown to indicate that a method has been passed an illegal or inappropriate argument.“

Frei übersetzt bedeutet dies, dass die NullPointerException für Fälle reserviert ist, in der ein Zugriff auf ein uninitialisertes Objekt unerwartet zur Laufzeit erfolgt. Genau gegensätzlich verhält es sich mit der Exception vom Typ IllegalArgumentException. Diese sollte immer verwendet werden, wenn eine explizite Validierung auf eine Variable, die für den Programmablauf notwendig ist, fehlschlägt.

Auch zur leichteren Fehlerbehebung ist eine IllegalArgumentException deutlich hilfreicher. Der Exception-Typ besitzt eine Konstruktorversion, welches die Angabe eines Hinweistextes ermöglicht. Dieser ist im Fehlerfall direkt am Stacktrace wiederzufinden und bietet dem Entwickler somit eine schnelle Anlaufstelle zur Problemlösung.

## null == order vs. order == null
Manche Java-Entwickler setzen das null-Literal immer absichtlich auf die linke Seite der Prüfung:

{% highlight java %}
if (null == order) {
 //...
}
{% endhighlight %}

Dieser Ansatz bietet dabei allerdings keinen Vorteil gegenüber der gebräuchlichen Version, bei der das Literal auf der rechten Seite der Prüfung steht. Beide Konstellationen sind absolut identisch und haben keinerlei Unterschiede im Bezug auf Performance und Sicherheit.
Vielmehr ist dies eine Vorgehensweise die in Programmiersprachen angewendet wird, bei denen eine versehentliche Zuweisung von null anstelle einer Prüfung möglich ist.
Diese versehentliche Verwechslung ist glücklicherweise in Java nicht möglich und fällt bereits zur Kompilierzeit auf:

{% highlight java %}
if (order = null) { // Kompilierfehler
  order.cancel();
}
{% endhighlight %}
