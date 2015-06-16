---
layout: post
title: "Tipps zur Realisierung von Clean Code – Teil 1"
---


Dieser mehrteilige Artikel verfolgt das Ziel, „Clean Code“-Praktiken möglichst anschaulich und verständlich zu vermitteln. Bevor es jedoch in die Tiefe von Clean Code geht, möchte ich kurz und prägnant auf die gewöhnliche Bedeutung jener Begrifflichkeit eingehen – Denn eine klare Definition, was man sich unter Clean Code vorstellen darf, gibt es nicht. Auch wenn es nicht eine allgemein anerkannte Definition gibt, versteht sich dennoch darunter meist ein möglichst eleganter, effizienter, robuster, verständlicher, leicht wartbarer und mit möglichst geringen Abhängigkeiten versehener Code.

<strong>Sprechende Bezeichnungen verwenden</strong>

Ganz gleich ob es um die Bezeichnung von Klassen, Klassenvariablen, Instanzvariablen, lokalen Variablen, Paketnamen oder Methoden geht, die Bezeichnung sollte stets sorgfältig gewählt werden.  Ein Softwareentwickler verbringt einen Großteil seiner Entwicklungsleistung mit dem Lesen bestehenden Codes. Ganz gleich ob es einen Bug zu fixen gilt, oder ein Feature entwickelt werden soll. Es sollte somit im Interesse eines jeden Entwicklers liegen, einen möglichst leicht lesbaren sowie selbsterklärenden Code zu implementieren. Die Bezeichnung von Klassen, Variablen und Methoden trägt hierzu einen wesentlichen Teil bei. Hierbei gilt, dass der Name möglichst erklären sollte, warum diese Variable/Klasse/Methode existiert, was es macht und wie es verwendet wird.

Anstelle etwa folgende Variablenbezeichnung zu nutzen
{% highlight java %}
int d; // elapsed time in days
     {% endhighlight %}
     
sollte stattdessen eher

{% highlight java %}
int elapsedTimeInDays
{% endhighlight %}

verwendet werden.
An dieser Stelle möge man vielleicht einwenden, dass es kein großer Unterschied sei, ob die Variable die Beschreibung enthält, oder ob ein Kommentar diese Aufgabe übernimmt. Der wesentliche Vorteil besteht jedoch darin, dass der Kommentar lediglich bei Deklaration der Variable steht. Bei jeder weitere Verwendung von „d“ im laufe des Codes, wird nicht auf dem ersten Blick ersichtlich, wofür diese Variable steht und was deren Absicht ist.


<strong>Keine Implementierungsdetails in Bezeichnungen aufnehmen</strong>
Gelegentlich fallen mir Variablen-Bezeichnungen auf, die Details zum zugrundeliegenden Datentypen offen legen oder zumindest zu Annahmen verleiten.

Ein Beispiel:
{% highlight java %}
Set<User> userSet = new HashSet<>();
{% endhighlight %}

Die Variable „userSet“ repräsentiert ein Set an User-Objekten. Man stelle sich nun vor, was passieren würde, wenn eines Tages die Implementierung von einem Set zu einer Liste (ArrayList) verändert werden würde? Im Idealfall würde zugleich die Variablenbezeichnung geändert werden. Falls dies jedoch nicht passiert, steigt die Wahrscheinlichkeit drastisch, dass ein Entwickler zukünftig, der die Deklaration nicht im Blickfeld hat, stets von einer Set-Implementierung ausgeht und darauf basierend falsche Mutmaßungen anstellt. Ein Einfallstor möglicher Bugs! Das Problem lässt sich jedoch einfach beheben, in dem schlichtweg keine technischen Implementierungsdetails in Namen aufgenommen werden.

{% highlight java %}
Set<User> userGroup = new HashSet<>();
{% endhighlight %}

<strong>Methodenimplementierungen möglichst kompakt halten</strong>

Je kompakter eine Methode ist, umso
- schneller lässt sich deren Absicht verstehen
- umso sprechender ist der zugehörige Methodenname
- umso geringer ist der Testaufwand von Unit-Tests

Gerade der letztgenannte Punkt wird häufig unterschätzt: Kompakte Methoden weisen normalerweise eine geringere Programmablaufkomplexität auf, als etwa umfangreichere Methoden.  Dies impliziert geringere Abhängigkeiten und somit einen geringeren Aufwand im „Mocking“.

Mit einem sprechenden Methodennamen und einer „knappen“ Implementierung wird die Grundlage für eine selbsterklärende Implementierung gebildet. Etwaige ergänzende Erklärungen in Form von inline-Kommentaren sollten somit weitestgehend entfallen.

Denken Sie in diesem Zusammenhang auch stets daran, dass eine Methode nur eine „Absicht“ besitzen sollte (Single-Responsibility-Prinzip).

<strong>Exzessive Nutzung von Methoden- und Konstruktorüberladungen meiden</strong>

Auch wenn das Überladen von Methoden und Konstruktoren mit Parametern auf den ersten Blick nach einem überaus nützlichen Feature aussehen mag, erweist es sich jedoch bei exzessiver Nutzung als ein Einfallstor möglicher Bugs. Während Methodensignaturen mit nur ein oder zwei Parametern meist noch verständlich sind, steigt die Komplexität im Verständnis jedoch schnell an, wenn drei oder mehr Parameter zum Einsatz kommen.

<strong>Boolean's als Parameter in Methodensignaturen meiden</strong>

Der alleinige Wert eines Boolean ist ohne zugehörig Kontext niemals aussagekräftig.

{% highlight java %}
refreshCart(true);
{% endhighlight %}

Erst wenn die Methode „refreshCart“ vom Entwickler im Detail betrachtet wird, offenbart sich die eigentliche Absicht. Es empfiehlt sich daher, entweder gänzlich auf die Überladung zu verzichten und eine separate Methode zu erstellen, oder einen „individuellen“ Datentypen zu erstellen.

<strong>Symmetrien und Analogien nutzen</strong>

Herausragendes Software-Design zeichnet sich unter Anderem dadurch aus, dass der damit konfrontierte Entwickler, diese ohne großartige Einarbeitungszeit möglichst intuitiv und korrekt verwenden kann. Als förderlich erweist es sich in diesem Zusammenhang auf ein symmetrisches Design zu achten.
Dies bedeutet im Detail, dass sowohl von der Implementierung, aber auch insbesondere von der Benamung entsprechend gegensätzliche Varianten vorliegen:

{% highlight java %}
loadFile();
saveFile();

showInformation();
hideInformation();

createUser();
deleteUser();
{% endhighlight %}

<strong>Unnötige Kommentare meiden</strong>

Grundsätzlich sollte die Absicht bestehen, stets Code zu schreiben, der möglichst aussagekräftig ist und keinerlei ergänzende Kommentare benötigt. Die Grundlage bilden hierzu sprechende Bezeichnungen und kompakte Methoden. Das Problem bei Kommentaren besteht darin, dass diese mit der Zeit schlichtweg veralten. Dies wiederum führt zu falschen Annahmen und dadurch zu Bugs. Aus diesem Grund sollten möglichst Kommentare (inbesondere Kommentare innerhalb von Methoden) gemieden werden.

<strong>Das „null“-Literal als Rückgabewert meiden</strong>

Der Name des Literal trägt bereits den Grund, warum es grundsätzlich keine gute Idee ist, „null“ als Rückgabewert in Methoden einzusetzen. „null“ ist schlichtweg vollkommen aussagefrei!

{% highlight java %}
myMap.get(key);
{% endhighlight %}

Liefert der Aufruf von „get“ null, kann dies bedeuten, dass unter dem Key zwar ein „Treffer“ vorliegt, der Wert aber nicht initialisiert ist, oder, dass unter dem angegebenen Key schlichtweg kein Eintrag in der HashMap existiert. Wird an dieser Stelle ein null-Handling implementiert und berücksichtigt hierbei der Entwickler nicht beide Fälle, kann dies zu unvorhergesehenen Fehlern führen.

Besser ist es dagegen, stets „echte“ Objekte zurückzugeben. Ggf. auch sogenannte „Null-Objekte“.


<strong>An „richtiger“ Stelle Fehler behandeln</strong>

Häufig wird in Java-Projekten stets das Fehlerhandling mittels try-catch-Konstrukt an jenen Stellen vorgenommen, an denen die Exception unmittelbar auftritt. Nur selten wird vom Keyword „throws“ Gebrauch gemacht und die Exception an eine „höhere“ Schicht übermittelt.

Immer, wenn es darum geht, Exceptions zu behandeln, sollte sich gefragt werden, an welcher Stelle vernünftigerweise auf diesen Fehler reagiert werden kann. Jene Stelle, die eine vernünftige Verwendung des Fehlerfalls aufweist, ist zugleich die Stelle, an der das Exception-Handling implementiert werden sollte. Gilt es Exceptions an höhere Schichten zu transportieren, kann ggf. auch ein „rethrow“ in Form einer abstrakteren Exception erfolgen.
