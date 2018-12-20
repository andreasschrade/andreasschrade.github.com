---
layout: post
title: "Java: Die null-Referenz (Teil I)"
category: "java"
permalink: /java/2013/08/12/java-die-null-referenz-teil-I/

---



## Hintergrund
In diesem Beitrag geht es - schlicht gesagt - einzig allein um das bekannte Java Literal <em>null</em>.

Die Thematik von <em>null</em> ist essentiell und stellt dennoch in einer Vielzahl an Lehrbücher lediglich eine Begleiterscheinung dar. Mir erscheint das Thema als besonders wichtig, da Missverständnisse im Umgang mit der null-Referenz, die Hauptursache für die häufigst verursachte Exception in Java darstellt:
Die Null-Pointer-Exception.

### Grundsätzliches zu null
Wenn Sie sich über den Hintergrund von <em>null</em> bereits bestens auskennen, können Sie diesen Absatz gerne überspringen. Andernfalls möchte ich Sie gerne dazu einladen, in die Tiefen der mysteriösen <em>null</em>-Referenz einzutauchen.

Bevor wir jedoch in die Erläuterung und Einführung des Themas richtig einsteigen, müssen wir zuerst grundlegendes zur Funktionsweise von Datentypen in Java klären:

Grundsätzlich wird in Java zwischen primitiven und komplexen Datentypen unterschieden. Zu den primitiven Datentypen gehören in Java int, double, char, boolean, long sowie float.
Alle andere Datentypen (z.B. String, BigDecimal) repräsentieren einen komplexen Datentypen und damit ein Objekt im Sinne der objektorientierten Programmierung (OOP).
Das spezielle Literal <em>null</em> spielt ausschließlich im Zusammenhang mit komplexen Datentypen (Referenvariablen) eine Rolle. Das null-Literal kann aufgrund seines typenlosen Zustandes jedem Objekttyp zugewiesen werden und verhält sich somit wie eine Art Untertyp (Subtype) einer jeden Klasse. Ähnlich wie die Klasse <em>Object</em> der Obertyp (Supertype) für jede Klasse in Java darstellt.

Primitive Datentypen besitzen als Standardwert immer einen Wert ihres jeweiligen Typ. Somit entweder 0, 0.0 oder false.

{% highlight java %}
int i; // i == 0
boolean b; // b == false
{% endhighlight %}

Bei komplexen Datentypen verhält es sich anders. Hier ist das null-Literal der implizite Standardwert für alle Referenzvariablen, sofern keine explizite Initialisierung über das new-Keyword stattfindet. Anstelle eines konkreten Wertes, wie es bei primitiven Datentypen (Primitives) gehandhabt wird (z. B. 42), speichert eine Referenzvariable somit lediglich als Wert den Verweis (Referenz) auf das eigentliche Datenobjekt (= dem Speicherbereich). Aus diesem Grund werden diese Datentypen auch Referenztypen bzw. Referenzvariablen bezeichnet.

{% highlight java %}
String s; // s == null
BigDecimal bd; // bd == null
{% endhighlight %}

Grundsätzlich ist dabei ein Objekt nicht an eine konkrete Referenzvariable gebunden. Wie wir bereits im vorherigen Kapitel zum Thema Copy-by-Referenz gesehen haben, können mehrere Referenzvariablen sich das gleiche Objekt „teilen“ bzw. darauf zeigen:

{% highlight java %}
Customer bruce = new Customer(„Willis“);
Customer bruce2 = bruce;
{% endhighlight %}

Beide Referenzvariablen teilen sich das identische Objekt, da beide Variablen als Referenz auf das in Zeile 1 angelegte Objekt verweisen. Aus diesem Grund spielt es keine Rolle, ob auf das Objekt mittels der Variable bruce oder bruce2 zugegriffen wird. 

## Legitimer Einsatz von null

Notwendig wird das null-Literal, wenn zum Zeitpunkt der Deklaration der letztliche Wert bzw. das Objekt noch unbekannt ist oder erst bei Bedarf initialisiert werden soll (= Lazy Loading).

{% highlight java %}
String title = null;
if (customer.isWoman()){
	title = „Frau“;
}else if (customer.isMan(){
	title = „Herr“;
}
customer.setTitle(title);
{% endhighlight %}

Zum Zeitpunkt der Deklaration der Variable <em>title</em> ist der letztliche Zielwert unbekannt, weshalb es die kurzzeitige Zuweisung von <em>null</em> erfordert.
Andernfalls, ohne der expliziten oder impliziten Zuweisung des null-Literal, wäre die Sichtbarkeit der Variable im Kontext der letztendlichen Verwendung nicht gegeben, sofern die Deklaration innerhalb der If-Bedingung stattfinden würde.
Die Zuweisung der null-Referenz wird in diesem Beispiel somit legitim eingesetzt.

Ein weiterer legitimer Einsatz von <em>null</em> ist der Ansatz, der sich hinter dem Design Pattern Lazy-Loading verbirgt. Die Grundidee hinter Lazy-Loading ist denkbar einfach. Verbirgt sich beispielsweise ein erhöhter Rechenaufwand in der Ermittlung eines Wertes (z.B. Kundenstatistik), so wird der Prozess der Evaluierung zum denkbar spätmöglichsten Zeitpunkt verlagert und nicht pauschal bei der Objekt-Erzeugung oder bei jedem Getter-Zugriff (Eager loading).
Im folgendem Beispiel ist der Gedanke hinter Lazy-Loading recht gut zu sehen.

{% highlight java %}
public class Customer {
	private CustomerStatistic statistic;
	public CustomerStatistic getStatistics() {
		// Initialisierung erfolgt bei erstmaliger Benutzung
		if (statistics == null) {
			statistics = calculateCustomerStatistic(this);
		}
		return statistics;
	}
}
{% endhighlight %}

Die Implementierung des Getters prüft, ob das Objekt <em>statistic</em> sich noch in einem uninitialisierten Zustand befindet. Dies ist lediglich bei erstmaligen Zugriff der Fall, wodurch eine einmalige Evaluierung des Wertes stattfindet. Im Anschluss erfolgt schließlich für die zukünftige Wiederverwendung die Zuweisung an die entsprechende Instanzvariable. 
Auch in diesem Fall ist der Umgang mit dem null-Literal vollkommen legitim.

## Vermeidbare Einsätze von null

Weniger anzustreben ist der absichtliche Einsatz von null als <strong>legitimer</strong> (regulärer Fall der mit Interaktion der Anwendung eintreten kann) Rückgabewert von Methoden. Häufig steht <em>null</em> als Rückgabewert in Methoden als Fehlerkennzeichen oder auch als Kennzeichen, dass für die fehlende Existenz eines angefragten Objekts steht.
Warum die null-Referenz als legitimer Rückgabewert einer Methode grundsätzlich nicht gut zu heißen ist und warum dieses spezielle Literal, wenn möglich, zu meiden ist, dass erfahren Sie im folgendem Abschnitt.

<strong>Warum null gefährlich ist</strong>

Tony Hoare, der „Erfinder“, der 1965 die null-Referenz erstmalig in der Programmiersprache Algol W implementiert hat, betitelt seine Erfindung als den größten Fehler seines Lebens.
Seiner Schätzung nach haben Softwarefehler (Null-Pointer-Exceptions) die anhand eines fehlerhaften Umgang mit null verursacht worden sind, bislang einen Schaden in Höhe von 1 Milliarden Dollar angerichtet.
Die einfache Implementierung des Literals, sowie den dadurch gewonnen Spielraum für den Entwickler klangen ursprünglich verlockend. Der Entwickler behält stets die Option selbst zu entscheiden, ob und wann eine Variable initialisiert werden soll.

Die Flexibilität birgt allerdings auch seine Schattenseiten. Immer dann, wenn in der Programmausführung der Zustand einer Variable nicht eindeutig geklärt ist, wenn somit null nicht ausgeschlossen werden kann, ist entsprechend mit einer Prüfung auf die null-Referenz zu reagieren:

{% highlight java %}
if (customer.getContactInformation() != null) {
	sendMail(customer.getContactInformation().getEmailAddress());
}
{% endhighlight %}

Ohne dem Null-Check würde es zu einer NullPointerException führen, wenn eine Mail an einen Kunden versendet werden soll, welcher keine Kontaktinformationen gepflegt hat.

Auf gar KEINEN FALL sollte dagegen das null-Risiko mithilfe eines try-catch-Konstrukts gelöst werden:

{% highlight java %}
try {
	sendMail(customer.getContactInformation().getEmailAddress());
} catch (Exception e) {
 // Nichts zu tun, da dies ein regulärer Anwendungsfall sein kann
}
{% endhighlight %}

Dieser Ansatz sieht anfangs durchaus zu Recht verlockend aus. Die Übersichtlichkeit ist vollkommen gegeben und im Falle einer Exception wird die entsprechende Fallback-Logik im catch-Block ausgeführt.

Die Version ist dennoch nicht zu empfehlen. Der klare Nachteil dieser Methodik liegt in der Art, wie try-catch Blöcke zur Laufzeit von der Java Virtual Machine gehandhabt werden. Grundsätzlich bedeutet Exception-Handling einen deutlich höheren Rechenaufwand, als wie die direkte, „ungesicherte“ Programmausführung. 
Deutlich günstiger als den Programmcode in einem try-Block zu kapseln, ist in diesem Fall die explizite Prüfung auf null.
Außerdem spricht gegen den Ansatz, der Missbrauch von try-catch Blöcken zur regulären Flusssteuerung. Dies wäre der Fall, wenn die null-Prüfung wie im  Beispiel zu sehen ist, realisiert werden würde. Die Zweckentfremdung eines try-catch Statements zu Flussteuerung widerspricht schließlich jeglicher guten Programmierpraktik!
Exception-Handling dient ausschließlich für Fehlerfälle oder Zustände, bei der keinerlei Möglichkeit auf eine Fehlerprüfung besteht.

## Gründe die gegen null als Rückgabe von Methoden sprechen

Ein guter Grund ist der aussagefreie Zustand des Literals. Null ist vollkommen aussagefrei und kann für mehrere Zustände sprechen.
Beispielsweise bei der Klasse <em>HashMap</em>.
Mittels der Methode <em>get</em> lässt sich anhand eines Schlüssels das zugehörige Wertepaar aus dem Objekt ermitteln:

{% highlight java %}
myMap.get(key);
{% endhighlight %}

Liefert der Methodenaufruf <em>null</em>, so kann dies entweder bedeuten, dass der zugehörige Wert des Key nicht initialisiert ist, oder, dass unter dem angegebenen Key kein Eintrag in der HashMap gefunden worden ist. 
Zwar gibt es zur Unterscheidung beider Fälle die Methode <em>containsKey</em>, welches explizit auf das Vorkommen des Key in der Map prüft, allerdings wird diese Methode selten zur Unterscheidung beider möglicher Fälle verwendet.

Diese Unterscheidung wäre allerdings zur Fehlerbehandlung und weiterem Programmablauf entscheidend. Es ist schließlich ein gravierender Unterschied, ob das gesuchte Element nicht vorhanden ist, oder aber sich in einem uninitialisierten Zustand befindet.

Methoden die <em>null</em> als Rückgabewert liefern, erfordern es jedem Aufrufer das Objekt auf die null-Referenz zu prüfen. Diese zusätzliche Prüfung geht auf Kosten der bereits mehrfach angesprochenen Code-Lesbarkeit und wird zudem gerne vergessen. Aus diesem Grund sollte immer wenn möglich <em>null</em> als Rückgabewert vermieden werden.

Ein Ansatz, <em>null</em> als Rückgabewert zu vermeiden, ist der Einsatz „echter“ Objekte mit sprechendem Fehlerwert sowie der Einsatz von speziellen Objektausprägungen, die immer dann zum Einsatz kommen, wenn <em>null</em> gesetzt werden würde (z. B. bei einem Fehlerfall).

{% highlight java %}
private String getFullname(){
 if(firstname != null && lastname != null{
  return firstname + “ “ + lastname;
 }
 return „“; // Empty String als Fallback anstelle von null
}
{% endhighlight %}

Das Null-Object-Pattern greift diesen Ansatz als Grundlage auf. Anstelle von <em>null</em> als Fehlerwert, wird eine echte Dummy-Instanz als Ersatz für das null-Literal eingesetzt:

{% highlight java %}
public interface Customer { 
  String getName();
	void setName(String name);
	 
	List<Account> getAccounts();
}
	 
public class NullCustomer implements Customer {     
    public String getName() {
        return "";
    }
     
    public void setName(String name) {}
	 
    public List<Account> getAccounts() {
        return Collections.emptyList();
    }
}
{% endhighlight %}

Abseits des Null-Objekt-Pattern gibt es aber weitere kleine "Kniffe", die die Komfortabilität mit <em>null</em> erleichtern:
Betrachten Sie hierzu bitte folgenden klassischen null-Check:

{% highlight java %}
if(customer != null){
  if(customer.isVIP(){
    return true;
  }
}
{% endhighlight %}

Wie lässt sich dieses Konstrukt kompakter und übersichtlicher gestalten?

Der erste naheliegende Schritt zur Optimierung ist die Zusammenfassung beider Bedingungen. Für diesen Zweck bietet sich der Short-Circuit UND-Operator an.

Ganz generell gibt es in Java zwei Short-Circuit-Operatoren.
Der Short-Circuit Und (&&) sowie der Short-Circuit Oder (||) Operator stellen in Java die gängige Weise zur logischen Verknüpfung von Bedingungen dar.
Die Funktionsweise von Short-Circuit ist dabei denkbar einfach:
Sind zwei Expressions über eine Short-Circuit-Oder-Verknüpfung verbunden, wobei die erste Expression auf true evaluiert wird, so ist das Gesamtergebnis bereits true, unabhängig davon auf welchen Wert der Operand auf der rechten Seite des Oder-Operator evaluiert wird.
Gleiches Verhalten gilt auch für den Short-Circuit-Und-Operator. Wird die erste Expression auf false evaluiert ist das Resultat unabhängig der zweiten Expression false. 
In beiden geschilderten Fällen wird somit nicht weiter auf die vom Operator rechtsstehenden Operanden eingegangen, da das Gesamtresultat allein durch das Ergebnis der Evaluation des ersten Operanden feststeht.
Dieses denkbar einfache Verhalten lässt sich für eine Vielzahl von Situationen nutzen.

Im Bezug auf das geschilderte Beispiel lässt sich, mit Einsatz der Short-Circuit UND-Verknüpfung, der Code vereinfachen:

{% highlight java %}
if(customer != null && customer.isVIP()){
    return true;
}
{% endhighlight %}

Praktischerweise führt die Bearbeitung der ersten Expression bereits zum Abbruch, wenn das Kundenobjekt null ist. Somit kann es bei dem geschilderten Beispiel zu keiner Null-Pointer-Exception kommen.


Die Anzahl der aneinandergereihten Short-Circuit-Verknüpfungen spielt dabei keine Rolle und kann nach Belieben gewählt werden. Folgend ein weiteres, typisches Code-Fragment, welches mit notwendigen Prüfungen auf die null-Referenz geprägt ist:

{% highlight java %}
public BigDecimal getTotalOrderCosts(Customer customer) {
	    if(customer != null) {
	        List<Order> orders = customer.getAllOrders();
	        if(orders != null) {
	            BigDecimal totalCosts = BigDecimal.ZERO;
	            for(Order order: orders) {
	                if(order != null) {
	                    totalCosts = totalCosts.add(order.getTotal());
	                }
	            }
	        }
	        return totalCosts;
	    } else {
	        return BigDecimal.ZERO;
	    }
	}
{% endhighlight %}

Die Prüfung des Kunden-Objekt, sowie dem Listen-Objekt und schließlich der Bestellungen erhöhen das Codevolumen und erschweren die Lesbarkeit. Ein erster Schritt zur Optimierung stellt die Gruppierung der Prüfungen auf null am Anfang der Methode dar:

{% highlight java %}
public BigDecimal getTotalOrderCosts(Customer customer) {
	    if(customer == null || customer.getAllOrders() == null) {
	        return BigDecimal.ZERO;
	    }
	    List<Order> orders = customer.getAllOrders();
	    BigDecimal totalCosts = BigDecimal.ZERO;
	    for(Order order: orders) {
	        if(order != null) {
	            totalCosts = totalCosts.add(order.getTotal());
	        }
	    }
	    return totalCosts;
	}
{% endhighlight %}

Mit Durchführung der zusammengefassten null-Prüfungen wird frühzeitig auf die Ausführungsmöglichkeit der eigentlichen Logik geprüft. Die eigentliche Geschäftslogik geht durch den aufgeräumten Code ersichtlicher hervor.


<strong>Hätten Sie gewusst,</strong>
…dass sich beide Short-Circuit-Operatoren auf Maschinensprachen-Ebene wie verschachtelte If-Bedingungen verhalten?
Somit verhalten sich beide Operatoren wie "Kontrollfluss"-Elemente, wodurch es keinen Unterschied in Bezug auf die Performance gegenüber mehrfach verschachtelten If-Anweisungen gibt.


[Weiter zum zweiten Teil der Serie]({% post_url 2013-08-22-java-die-null-referenz-teil-II %})