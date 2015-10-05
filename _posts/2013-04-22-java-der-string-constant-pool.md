---
layout: post
title: "Java: Der String-Constant-Pool"
category: "java"
---



## Hintergrund
Eine Besonderheit der Klasse String ist die Art, in der die Initialisierung erfolgen kann. 
Welche Arten es gibt und welche Auswirkung diese in Hinblick auf Performance besitzen, erfahren Sie im folgenden Abschnitt:

### Der String-Constant-Pool
Neben der Erzeugung über das Keywoard <em>new</em> - wie es normalerweise für komplexe Datentypen üblich ist, ist ebenso der direkte Weg über den Zuweisungsoperator möglich. Eine Besonderheit die sonst nur bei primitiven Datentypen angewendet werden kann:

{% highlight java %}
String a = new String("myString");
String b = "myString";
{% endhighlight %}

Doch worin liegt der Unterschied und welcher Art der Initialisierung sollte bevorzugt werden?

Mit betrachten des Speicherverbrauchs komplexer werdender Applikation machen meist String-Objekten einen großen Anteil am Gesamtspeicherbedarf aus. Ursache ist das hohe Vorkommen mehrfacher vorkommener String-Objekte die vollkommen identisch sind.

Ersichtlich wird das Zustandekommen doppelter Objekte beispielsweise mit Betrachten einer Instant-Messenger Applikation. Im Chat-Fenster, in dem der Verlauf der Nachrichten angezeigt wird, wird üblicherweise neben der Uhrzeit der Nachricht auch die zugehörige Person bzw. der Nickname des Benutzers angezeigt. Bei einem längeren Verlauf könnte allein hierbei problemlos 50 bis 100 identische String-Objekte allein zur Anzeige des Namen (z. B. „Paul“) erzeugt werden (z. B. für ein Text-Label-Element).

Dies ist allerdings ein unnötiger Vorgang, da - wie wir bereits gelernt haben – String-Objekte nicht nachträglich verändert werden können. Aus diesem Grund besitzen String-Objekte die ideale Vorrausetzung zur Nutzung eines Caching-Mechanismus, welches bei Bedarf, wenn eine neue Zeichenkette angefragt wird, die bereits im Cache befindliche Instanz verwendet anstelle pauschal ein neues Objekt zu erzeugen.
Es ist deshalb wenig verwunderlich, dass genau ein solcher Caching-Mechanismus in Java bereits existiert:

Unter den Namen String-Constant-Pool stellt die VM einen Konstanten-Cache zur Verfügung, indem String-Objekte gesammelt werden. Wird eine neue Zeichenkette angefordert, wird geprüft ob das Objekt bereits im Pool existiert. Im Falle einer Übereinstimmung wird die Referenz auf das bestehende Objekt im Cache zurückgeliefert. Andernfalls, wenn das Objekt sich noch nicht im Cache befindet, wird ein neues String-Objekt angelegt und in dem Konstanten.Pool aufgenommen.

Leider werden dabei nicht alle String-Objekte in den Konstanten-Pool aufgenommen. Bis einschließlich Java 7 beschränkt sich dieser Cache lediglich auf Zeichenketten, die <strong>im Programmcode</strong> in String-Literalen angegeben und zugewiesen worden sind (=somit zur Kompilierzeit existieren). String-Objekte die mittels dem Keywoard new erzeugt werden, sind somit von den Vorzügen des String-Constant-Pool nicht betroffen und finden somit keinerlei Berücksichtigung im Caching-Mechanismus.

Aus diesem Grund ist immer die direkte Zuweisung von Zeichenketten über das String-Literal der Objekt-Erzeugungung mittels dem Keyword new vorzuziehen:

{% highlight java %}
String a = new String("myString"); // Kein Caching
String b = "myString"; // Caching erfolgt! Best Practice
{% endhighlight %}

### String-Internalisierung
Um auch in den Genuss des Konstanten-Pools für String-Objekte zu kommen, die nicht zur Kompilierzeit bekannt sind (z.B. aus einer Datenbank dynamisch geladen werden), gibt es die Möglichkeit zur sogennanten String-Internalisierung.

Diese wird recht einfach mit Aufruf von <em>intern()</em> des jeweilig betroffenen String-Objekts ausgeführt und hat den Effekt, dass der String, sofern dieser bereits im Pool existiert, daraus entnommen wird.
Existiert der String dagegen noch nicht im Pool, wird dieser entsprechend hinzugefügt:

{% highlight java %}
String a = "myString";
String b = "myString".intern();
String c = new String("myString");
String d = new String("myString").intern(); 
{% endhighlight %}

Bei der Variablenzuweisung von a erfolgt die Stringerzeugung über das String-Literal. Hierbei wird der String automatisch in den Konstanten-Pool aufgenommen, da die Zeichenkette bereits zur Kompilierzeit vorliegt.
Auch die Zuweisung der Variable b erfolgt über das String-Literal. Der zusätzliche Aufruf der Methode intern ist jedoch unnötig und ohne Effekt, da bereits allein mit verwenden des Literals, das Objekt in dem Pool aufgenommen wird.
Bei Variable c und d wird jeweils ein neues Objekt angelegt. Dies sieht die JVM-Spezifikation vor, dass Objekte die mittels dem Keyword new erzeugt werden, niemals gecachede Werte repräsentieren. Der Unterschied zwischen c und d liegt im nachträglichen Aufruf der Methode intern(). 
Der zusätzliche Aufruf bei d weißt die VM an, das String-Objekt in den Konstanten-Pool aufzunehmen. Da dieses bereits im Pool seit der Erzeugung von a existiert, erhält die Variable d somit die Referenz auf das bereits gecachede String-Objekt aus dem String-Constant-Pool.

Würden die String-Objekte mittels des Vergleichsoperators anschließend verglichen werden, so hätte dies folgendes Ergebnis:

a == b // true <br>
a == c // false    <br>
a == d // true         <br>

Die Variablen a, b und d teilen sich die gleiche Referenz auf das identische String-Objekt im Konstanten-Pool. Lediglich c hält ein separates Objekt und kann somit nicht ohne Einsatz der Methode equals verglichen werden. Da c über new erzeugt worden ist und anschließend nicht internalisiert wurde, ist dies wenig verwunderlich.

Wenn sichergestellt werden kann, dass alle String-Objekte entweder direkt über das Literal oder via internal() internalisiert werden, so kann bedenkenlos der Vergleich von Zeichenketten in der gesamten Applikation über den Vergleichsoperator erfolgen.

Übrigens, seit Java 7 befindet sich dieser Konstanten-Pool im Heap und damit nicht länger im PermGen.