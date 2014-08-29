---
layout: post
title: "Java: Statische Initialisierung und Double-Brace Syntax"
---



## Statische Initialisierung
Ein static initializer setzt sich lediglich aus dem Keyword static, sowie zwei geschweiften Klammern zusammen. Innerhalb des Klammernblocks können ein oder mehrere Anweisungen erfolgen. In der Regel erfolgt in statischen Initialisierungsblöcken – wie der Name bereits verrät - die Initialisierung von Klassenvariablen. Sobald der Classloader die Klasse lädtm werden sämtliche statische Initialisierungsblöcke einmalig ausgeführt.

Existieren mehrere solcher static initializer innerhalb einer Klasse, so erfolgt die Bearbeitungsreihenfolge strikt von oben nach unten, gemäß der Reihenfolge wie diese im Quellcode platziert sind.
Würden folgend abgebildete Klasse mit

{% highlight java %}
Price price = new Price();
{% endhighlight %}

Aufgerufen werden, so würde auf der Konsole die Zahlenfolge 132 ausgegeben werden.

{% highlight java %}
class Price{
  static {
    System.out.print(“1”);	
  }
  
  public Price(){
    System.out.print(“2”);
  }

  static {
    System.out.print(“3”);	
  }
}
{% endhighlight %}

Auf das Beispiel mit der Währungsliste bezogen, lässt sich unter Verwendung der statischen Initialisierungsblöcke die Befüllung der Liste deutlich eleganter lösen:

{% highlight java %}
class Price {
	private static List<String> currencies = new ArrayList<String>(4);

	static {
		currencies.add("Euro");
		currencies.add("Dollar");
		currencies.add("Pfund");
		currencies.add("Yen");
	}
	
	Price(BigDecimal netPrice, int productCode) {
	...
}
{% endhighlight %}

Wohlgleich static initializer grundsätzlich ihre klare Daseinsberechtigung besitzen und beispielsweise statische Collections in der Praxis sehr häufig auf diese Weise befüllt werden, so ist dies auf unsere Beispiel bezogen jedoch noch immer nicht die idealste Lösung.

Anstelle eines statischen Initialisierungsblockes wäre auch eine einfache statische Methode denkbar, die sowohl die Objekt Initialisierung, als auch alle darauf aufbauenden Anweisungen kapselt:

{% highlight java %}
private static List<String> currencies = createCurrenciesList();

private static ArrayList<String> createCurrenciesList() {
	currencies = new ArrayList<String>;
  currencies.add("Euro");
	currencies.add("Dollar");
	currencies.add("Pfund");
	currencies.add("Yen");
	return currencies;
}
{% endhighlight %}

Diese Variante hat gegenüber den statischen Initialisierungsblöcken den Vorteil, dass sich die Ausführung jederzeit gezielt wiederholen lässt und die Absicht der gekapselten Anweisungen, dank des Namen der Methode, bereits selbsterklärt. Ein statischer Initialisierungsblock benötigt dagegen immer eine Beschreibung, da andernfalls die Code-Verständlichkeit fehlt bzw. ein mühselige durcharbeitet sämtlicher Anweisungen erfordert.

Der nächste Ansatz führt uns nun zu einem versteckteren bzw. wenig bekannteren Sprachmittel von Java. Dem sogenannten Double-Brace Syntax:

## Der Double-Brace Syntax
Während es für Arrays das bereits in einem früheren Blog-Post kennen gelernte Literal zur Initialisierung und Zuweisung von Elementen gibt, existiert für Collection und Map noch nichts Derartiges. Ursprünglich war für Java 7 ein solches Literal vorgesehen, welches nun aber auf dem Nachfolger Java 8 verschoben wurde.
Die Idee hinter den im Proposal „Project Coin“ beschriebenen Literal ist selbsterklärend:

{% highlight java %}
// List: Java 6 / Java 7
List<String> list = new ArrayList<String>();
list.add("item");

// List: Java 8
List<String> list = ["item"]; 


// Map: Java 6 / Java 7
Map<String, String> map = new HashMap<String, String>();
map.put("key", “value”);

// Map: Java 8
Map<String, Integer> map = {"key" : “value”};
{% endhighlight %}

Als „Ersatz“ findet deshalb des Öfteren die Befüllung von Collections mithilfe des Double-Brace Syntax statt:

{% highlight java %}
class Price {
  private static List<String> currencies = new ArrayList<String>(4){{
  add("Euro");
  add("Dollar");
  add("Pfund");
  add("Yen");
 }};
{% endhighlight %}

Der Double-Brace Syntax ist dabei kein spezielles Sprachmittel zur Befüllung von Collections, vielmehr wird der Ansatz einer anonymen inneren Klassen hierbei „zweckentfremdet“. Ein „Syntax“, der daher nur auf dem ersten Blick befremdlich wirkt.
Mit dem ersten Klammerpaar nach dem Konstruktor wird eine anonyme innere Klasse erzeugt:

new ArrayList<String>(4){ }

Das direkt folgende zweite Klammerpaar definiert lediglich einen instance initializer  Codeblock. Ein instance initializer ist im Prinzip der Pendant zum static initalizer mit dem Unterschied, dass dieser bei jeder Objekt-Erzeugung aufgerufen wird. Der static initializer block wird, wie bereits eingangs des Abschnitts erwähnt, dagegen nur einmal ausgeführt, nämlich dann wenn der Classloader das Laden der Klasse abgeschlossen hat. Auch beim instanzbezogenem Initialisierungsblock spielt es keine Rolle, wie viele Blöcke definiert werden. Alle instance initializer werden bei der Objekt-Erzeugung vor Aufruf des Konstruktors, aber nach Aufruf des Konstruktors der Superklasse entsprechend der Reihenfolge im Quellcode von oben nach unten ausgeführt.

In einem Initializierungsblock existieren dabei keinerlei Einschränkungen welches Anweisungen in Bezug auf Instanzvariablen und Methoden durchgeführt werden können. Auf das Beispiel bezogen bedeutet dies, dass eine anonyme innere Klasse, die von ArrayList ableitet erzeugt wird, welches in einem initializierungs-block die add-Methoden der Superklasse ArrayList aufruft.

{% highlight java %}
new ArrayList<String>(4) { // Anonyme innere Klasse
  { // Instanzbezogener Initialisierungblock
    add("Euro"); // Aufruf Instanzmethode
    
  }
}
{% endhighlight %}

Der Syntax lässt sich somit nicht nur für Collections anwenden. Der Double-Brace-Syntax eignet sich für jegliche Objekt-Erzeugung unter der Voraussetzung, dass die zu instanziierende Klasse nicht als final deklariert ist.
Ist eine Klasse als final deklariert kann keine Unterklasse erzeugt werden und die Double-Brace Initialisierung schlägt fehl.

Naheliegend ist der Einsatz der Syntax bei Klassen, deren Konstruktor nur eine eingeschränkte Auswahl der zu setzenden Attribute abdeckt. Dies hat zur Folge, dass mit einer Abfolge von Setter-Aufrufen die fehlenden Eigenschaften nachgereicht werden:

{% highlight java %}
Car myCar = new Car(„Taxi“);
myCar.setWidth(1.82f);
myCar.setHeight(1.622f);
myCar.setColor(Color.WHITE);
myCar.setMaxSpeed(184);

// Double Brace
Car myCar = new Car(“Taxi”){{
  setWidth(1.82f);
  setHeight(1.622f);
  setColor(Color.WHITE);
  setMaxSpeed(184);
}};
{% endhighlight %}

Ist Double-Brace somit das ideale Mittel zur unkomplizierten Objekterzeugung?
Leider nein. So spannend der Ansatz auch aussehen mag, aber leider birgt die scheinbar leichtgewichtige und schlanke Funktionalität auch diverse Nachteile.

Mit betrachten der exemplarisch ausgeschriebenen Langform von Double-Brace 

{% highlight java %}
class AnonymInnerClass extends ArrayList {
	{ // Instanbezogener Initialisierungblock
		add(„Euro“); // Methodenaufruf der Superklasse
	}
}
{% endhighlight %}

wird ersichtlich, dass nicht nur eine Instanz je Double-Brace Initialisierung entsteht, sondern eine eigene individuelle Klasse erzeugt werden muss. Dies bedeutet, dass ein Objekt, welches via Double-Brace erzeugt worden ist, auch den Overhead einer regulären Klasse verursacht. Darunter fällt beispielsweise eine eigene Class-Datei, die entsprechend von der VM behandelt werden muss. Werden 3 Objekte via Double-Brace erzeugt, fallen neben der Class-Datei der Basis-Klasse drei weitere Class-Dateien an. Kein Aspekt der gerade bei intensivem Einsatz der kurzen Syntax sich vorteilhaft im Bezug auf die Performance auswirkt. 

Ein weiterer Nachteil den es vor allem bei der Verwendung von Double-Brace zu berücksichtigen gilt, ist eine ständig drohende Objekt-Inkompatibilität mit „regulär“ initialisierten Objekten.
Dieses Inkompatibilitätsproblem entsteht durch das implizite subclassen und macht sich konkret bei Methoden bemerkbar, deren Implementierung eine Typprüfung vorsieht. 
Beispielsweise bei folgender gängigen Implementierung der Methode equals:

{% highlight java %}
@Override
public boolean equals(final Object o) {
  if (o == null) {
    return false;
  } else if (!getClass().equals(o.getClass())) {
    return false;
  } else {
    Example other = (Example) o;
    // Wertevergleich…
  }
}
{% endhighlight %}

Knackpunkt ist der Vergleich auf die Typkompatibilität beider zu vergleichender Objekte mittels der Methode getClass.  Werden auf Basis dieser Implementierung zwei Objekte verglichen, wobei nur bei einem Objekt der Double-Brace-Syntax angewendet worden ist, so können beide Objekte niemals gleich sein, da die Typprüfung fehlschlägt:

{% highlight java %}
public class CarEqualsTest {
  public static void testEquality(){
    // „Normale“ Initialisierung
    Car car1 = new Car("Taxi");
    car1.setWidth(1.82);
		
    // DoubleBrace Initialisierung
    Car car2= new Car("Taxi"){{ 
	setWidth(1.82);
    }};
    
    System.out.print(car1.getClass()); // com.vehicles.Car

    System.out.print(car2.getClass()); // com.vehicles.CarEqualsTest$1
  }
}
{% endhighlight %}

Natürlich hängt es von der konkreten Implementierung der Methode equals ab, ob die Double-Brace-Initialisierung Einfluss auf den Typvergleich nimmt.
Beispielsweise funktioniert der Objektvergleich von List implementierenden Datentypen auch unter Verwendung der Double-Brace-Syntax. Die Funktionstüchtigkeit bleibt bestehen, da die equals-Methode der AbstractList-Klasse lediglich prüft, ob das zu vergleichende Objekt kompatibel mit dem Datentyp List ist. Dies ist praktisch immer der Fall, wenn die Klasse selbst, oder eines der Supertypen das besagte Interface implementiert (z.B. ArrayList). Da Objekte die mit Double-Brace-Syntax erzeugt worden sind auch nur Subtypen von List darstellen, funktioniert der Typvergleich im diesem Beispiel aufgrund des gemeinsamen Interfaces nach wie vor. Der Kompatibilitäts-Aspekt ist neben den Performanceinbußen einer der wesentlichen Punkte, die bei Verwendung der Double-Brace-Initialisierung bedacht werden sollten. 
Im Bereich der Testentwicklung, in der Performance eine weniger gewichtige Rolle spielt, kann getrost - sofern keine Kompatibilitätsprobleme bestehen - auf Double-Brace zur Erstellung von Mock-Objekten zurückgegriffen werden. 

Letztendlich stellt der Double-Brace-Syntax auch keine zufriedenstellende Lösung für das geschilderte Problem dar. Die weitere Suche führt uns nun zu der im Utility-Package platzierten Klasse Arrays.

Die Klasse Arrays ist eine nicht instanzierbare und mit öffentlichen Klassenmethoden ausgestattete Utility-Klasse, welche allerlei Funktionen im Umgang mit Arrays zur Verfügung stellt. Jede Utility-Klasse sollte grundsätzlich keine Möglichkeit zur Instanzierbarkeit geben. Um dies zu erreichen, reicht es den Konstruktor der Utility-Klasse nicht public, sondern auf private zu setzen.

In der Utility-Klasse Arrays befindet sich unter Anderem die nützliche Methode asList. Diese Methode besitzt lediglich die Absicht, aus einzelnen Argumenten sowie anhand eines Arrays, eine Liste zu erzeugen:

{% highlight java %}
static List<String> currencies = Arrays.asList("Euro", "Dollar", "Pfund", “Yen“));
{% endhighlight %}

Die Liste über diesen Weg zu erzeugen ist deutlich klarer, erzeugt nicht den Overhead der Double-Brace-Initialisierung in Form einer inneren Klasse, wird lediglich einmalig aufgerufen, besitzt vollkommene Typsicherheit zur Kompilierzeit und ist zudem kompakt in der Schreibweise.
Weiter kürzen lässt sich die Schreibarbeit, wenn die Utility-Klasse mittels statischen Import eingebunden wird.
