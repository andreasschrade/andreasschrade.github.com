---
layout: post
title: "Java: Der Conditional-Operator im Detail"
---



## Hintergrund
Obwohl der Conditional-Operator bereits seit Anbeginn von Java vertreten ist, wird dieser dennoch verhältnismäßig spärlich in Java Projekten eingesetzt. Weit verbreitet ist die Ansicht, dass der Einsatz des Operators zu schwer lesbaren Code führt und dabei keinen nennenswerten Mehrwert gegenüber If-Statements bietet.
Ob dieses Schattendasein dem Conditional-Operator gerecht wird, oder ob es entgegen vieler Vorurteile denkbare Szenarien gibt, mit dessen Hilfe sich sogar die Lesbarkeit verbessern lässt, dass erfahren Sie in diesem Artikel.

### Einführung
Sollte ihnen die Funktionsweise des Operators bekannt sein, können Sie gerne diesen Abschnitt überspringen und mit folgendem fortfahren.

Grundsätzlich besteht der Operator immer aus drei Expressions, die durch ein Fragezeichen sowie einem Doppelpunkt separiert werden:

Expression (Bedingung) ? Expression (true) : Expression (false)

Die erste Expression muss zu einem booleschen Wert (true/false) evaluiert werden und stellt die Bedingung dar. Wird die Bedingung auf true evaluiert, so trifft die Bedingung zu und es wird die (zweite) Expression (nach dem Fragezeichen) ausgeführt. Trifft die Bedingung nicht zu, wird dagegen die dritte Expression (nach dem Doppelpunkt) evaluiert. Dabei darf jedoch weder die zweite noch die dritte Expression zu void evaluiert werden. Das bedeutet, dass nach der Evaluierung ein konkreter Wert vorhanden sein muss, wodurch beispielsweise Aufrufe von Methoden ohne Rückgabewert ausscheiden:

{% highlight java %}
1 == 1 ? 42 : 43; // 42 
1 == 1 ? System.out.println("42") : 43; // Kompilierfehler
1 == 1 ? System.currentTimeMillis() : 43; // aktuelle Zeit
{% endhighlight %}

Der Conditional-Operator liefert somit immer ein Resultat. Entweder über die Expression die bei einer true-Evaluierung ausgeführt wird, oder über die Evaluierung der false-Expression. Der Conditional-Operator benötigt somit immer einen „else“-Block und kann nicht wie die klassische If-Bedingung auf eine Kurzform ohne „else“-Zweig verkürzt werden.
Die nicht weniger gebräuchliche Begrifflichkeit Ternary-Operator verdeutlicht dies, da diese Art an Operator, drei Operanden voraussetzt (ternary -> dreifach).

<strong>Beispiel</strong>
Die Klasse Order liefert dem Aufrufer mit der Methode getDeliveryAddress() die zu einem Kundenauftrag zugehörige Lieferadresse. Diese kann, unter Umständen, von der Rechnungsadresse abweichen und wird in der Applikation zum Ausdruck der Versandetiketten benötigt.
Konkret liefert die Methode ein Objekt vom Typ Address, welches alle relevanten Daten wie Name, Vorname, Straße und Ort kapselt.
Wurde vom Kunden keine abweichende Addresse angegeben, an der das Paket versendet werden soll, so wird automatisch das gespeicherte Address-Objekt der Rechnungsadresse verwendet. Auf das Datenmodel wirkt sich die fehlende Auswahl einer expliziten Lieferadresse durch eine null-Referenz der Variable deliveryAdress aus.

Wie würden Sie die aktuelle Implementierung optimieren?

{% highlight java %}
public Address getDeliveryAddress() {
  Address deliveryAddress;
  // DeliveryAddress Objekt auf null prüfen
  if(this.getDeliveryAddress() != null){
   // Lieferadresse wurde vom Kunden hinterlegt
   deliveryAddress = this.getDeliveryAddress();
  }else{
   // Objekt ist nicht initialisiert. Rechnungsadresse verwenden
   deliveryAddress = getPaymentAddress();
  }
  
  return deliveryAddress;
}
{% endhighlight %}

Das Beispiel zeigt ein einfaches null-Handling Szenario, dem häufigsten Einsatzzweck des Conditional-Operator. 
Null-Prüfungen bei der eine Wertzuweisung auf einen Default oder Fallback-Wert erfolgt, sind ein typisches Beispiel, welches sich mithilfe des Conditional-Operator geschickt abwickeln lässt:

deliveryAddress = getDeliveryAddress() != null ? getDeliveryAddress() : getPaymentAddress());

Als Bedingung agiert in diesem Beispiel die Prüfung auf die null-Referenz des Adress-Objekt. Ist das Address-Objekt initialisiert bzw. liefert die Methode getDeliveryAdress nicht die null-Referenz, so wird die Expression nach dem Fragezeichen-Operator ausgeführt (getDeliveryAddress()).
Dies hat schließlich zur Folge, dass nach der Evaluierung die zurückgelieferte Objekt-Referenz der lokalen Variable deliveryAdress zugewiesen wird.
Wird dagegen die Bedingung auf false evaluiert, so wird als Fallback der Wert der dritten Expression (getPaymentAdress()) der lokalen Variable zugewiesen.
Der Datentyp der Variable auf der die Zuweisung erfolgt, muss dementsprechend kompatibel mit den evaluierten Werten aus der zweiten und dritten Expression sein.

Gerade bei längeren Conditional-Anweisungen bietet es sich an, zur Erhöhung der Lesbarkeit, das Statement in mehrere Zeilen aufzuteilen.
Bewährt hat sich dabei zur Erleichterung der Code-Lesbarkeit die Aufteilung in einzelne Code-Zeilen wie folgt:

{% highlight java %}
address= (getDeliveryAddress() != null) // Objekt initialisiert?
       ? getDeliveryAddress()  // initialisiert
       : getPaymentAddress()); // nicht initialisiert
{% endhighlight %}

Auch hier sollten Sie Vorsicht walten lassen und zu lange Conditional-Statements vermeiden. Die Aufteilung in Bedingung, true sowie false-Zweig in einzelne Zeilen, die jeweils abschließend gegebenenfalls mithilfe eines Kommentars die Erklärung liefert, sind dabei noch ein guter Kompromiss.

Während das vorhergehende Beispiel, ein weitläufiges Paradebeispiel für den Einsatz des Conditional-Operators darstellt, bietet der Conditional-Operator darüber hinaus noch weitere Vorzüge.

### Weitergehende Anwendungsmöglichkeiten
Für gewöhnlich ist der Conditional-Operator auf der rechten Seite einer Zuweisung wiederzufinden.
In folgendem Beispiel verhält es sich jedoch grundlegend anders, da je nachdem, wie die Bedingung zutrifft, unterschiedliche Objekte verwendet werden müssen.

(retryOrderSubmit(order)?orderQueue:deadLetterQueue)

Das fiktive Beispiel beinhaltet eine Verzweigung, die darüber entscheidet, ob ein Kundenauftrag in die "reguläre" Queue zur weiteren Verarbeitung geschrieben wird, oder aber, wenn mehrere Verarbeitungsversuche fehlgeschlagen sind, in die DeadLetterQueue wandert.

Nach Auflösung der Bedingung bleibt schließlich eine Objekt-Referenz als evaluiertes Ergebnis übrig.
Die äußere Klammerung wird bei diesem Prozess aufgelöst, was zur Folge hat, dass reguläre Zugriffe auf die Instanz mittels dem bekannten Punktseparator erfolgen können:

(retryOrderSubmit(order)?orderQueue:deadLetterQueue).dispatch(order);

Die Voraussetzung zur Verwendung des Conditional-Operator bleibt: Die Bedingungsexpression muss zu einem Wert vom Typ boolean evaluiert werden sowie das Resultat der zweiten und dritten Expression müssen zueinander typkompatibel sein.

Welche Variante (Conditional-Operator, If-Statement) konkret auf das Beispiel bezogen die meisten Vorteile bietet, hängt in diesem Falle vor allem von den jeweiligen Vorlieben des Entwicklers ab. Der Einsatz des Conditional-Operator ermöglicht Code auf Kosten der Lesbarkeit in einer kompakten Form zu verpacken. Kürzerer Code bedeutet eine erhöhte Aufmerksamkeit und stärkerer Fokus auf das Wesentliche.

Nach einer kurzen Gewöhnungsphase liest sich auch ein vom Conditional-Operator geprägter Code ohne jeglichen Mehraufwand flüssig.
Der Weisheit letzter Schluss liegt sicherlich irgendwo zwischen dem puren Einsatz des Bedingungsoperator und dem ausschließlichen Einsatz von If-Bedingungen. Es gibt Einsatzgebiete da empfiehlt es sich dringend auf die kompaktere Schreibweise zurückgreifen. Ebenso gibt es Fälle, indem es vermieden werden sollte, etwaig mögliche Konstrukte mithilfe des Conditional-Operators zu realisieren.

Folgend ein Beispiel, welches die erhöhte Verständlichkeit des Codes mit Einsatz des Ternary-Operator verdeutlicht. Die Variante, die auf dem Conditional-Operator beruht, ist vollkommen selbsterklärend und benötigt zudem, als kleinen Bonus, keine zusätzliche lokale Variable:

{% highlight java %}
class MoneyFormatter {
private static DecimalFormat formatter = new DecimalFormat("0.00");

	// Klassische Implementierung
	public static String formatCurrencyToString(BigDecimal amount) {
		String returnValue = "0,00";
		if(amount != null) {
			returnValue = formatter.format(amount);
		}
		return returnValue;
	}
	
	// Implementierung auf Basis des Conditional Operator
	public static String formatCurrencyToString2(BigDecimal amount) {
		return amount != null
				? formatter.format(amount)
				: "0,00";
	}

}
{% endhighlight %}

### Mögliche Stolperfallen
Es gibt allerdings auch Stolperfallen, die es im Umgang mit dem Bedingungsoperator zu berücksichtigen gilt. Gerade in Hinblick auf die Prioritätsreihenfolge der Operatoren, die in einem älteren Beitrag erwähnt wurden, gibt es potentielle Fehlerfälle.

Was wird auf der Konsole ausgegeben, wenn die Methode mit
{% highlight java %}
formatCurrency(null)
{% endhighlight %}
aufgerufen wird?
{% highlight java %}
public static String formatCurrency(BigDecimal amount){
	return "Euro " + amount != null ? amount.toPlainString() : "0,00";	
}
{% endhighlight %}

Eigentlich sollte davon ausgegangen werden, dass die null-Prüfung erfolgt und entsprechend der Wert „Euro 0,00“ zurückgegeben wird.
Stattdessen jedoch beendet sich das Programm mit einer Null-Pointer-Exception.
Ursache ist die bereits angesprochene Prioritätsreihenfolge nach der die Reihenfolge der Abarbeitung der Operatoren geregelt wird. Diese sieht für den Plus-Operator eine höhere Priorität, als wie dem Conditional-Operator sowie dem Vergleichsoperator (!=), vor. Dies hat zur Folge, dass zuerst die String-Verkettung stattfindet („Euro null“) und anschließend dieser String auf null geprüft wird. Da dies nicht zutrifft, erfolgt schließlich der Zugriff auf die uninitialisierte Referenzvariable. Dies wird prompt mit einer Null-Pointer Exception quittiert.

Lösen lässt sich diese Problematik unter Verwendung entsprechender Klammerungen.
Wird die eigentliche Bedingungsexpression in Klammern gesetzt, so ist die richtige Funktionsweise hergestellt, da zuerst die Conditional-Expression aufgelöst wird, bevor die String-Verkettung stattfindet:

{% highlight java %}
return "Euro " + (amount != null ? amount.toPlainString() : "0,00");
{% endhighlight %}

### Der Performance-Mythos
Als letzte Anmerkung zum Thema Bedingungsoperator, sei darauf hingewiesen, dass bei der Entscheidungswahl zwischen Bedingungsoperator und If-Statement die Performance keine Rolle spielt. Tatsächlich gibt es keinen Unterschied zwischen dem Bedinungsoperator und dem If-Statement im Bezug auf die Performance.
