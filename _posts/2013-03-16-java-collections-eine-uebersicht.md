---
layout: post
title: "Java: Die Collection-Implementierungen im Detail"
category: "java"
---



## Hintergrund
Das flexiblere und komfortablere Pendant zum Array ist die Collection.
Die Collection ist aus technischer Sicht lediglich ein Interface, welches verschiedene konkrete Ausprägungen für jeden Einsatzweck im Umgang mit typgleichen Objekten ermöglicht.
Die wesentlichen 3 Interfaces, die wiederum von diesem „Super“-Interface ableiten, sind List, Set und SortedSet.

Auf Basis dieser Interfaces gibt es jeweilig konkrete instanzierbare Klassen:

<strong>List:</strong>
LinkedList, ArrayList, Vector

<strong>Set:</strong>
HashSet, LinkedHashSet, TreeSet

<strong>SortedSet:</strong>
TreeSet

### List, Set und SortedSet im Vergleich
Für die meisten Anwendungsgebiete wird als Collectiontyp eine Klasse verwendet, die entweder das Interface List oder Set implementiert. Auf lediglich zwei Punkten reduziert, lässt sich bereits der wesentliche Unterschied zwischen beiden genannten Interfaces sowie deren Absicht darstellen:
 
Grundsätzlich stellt eine List im Gegensatz zum Set eine sortierte Reihenfolge der Elemente dar. Dies bedeutet, dass der Anwender konkret die Reihenfolge festlegen kann, in der die Elemente gespeichert werden. Der Entwickler hat somit jederzeit die vollständige Kontrolle, an welcher Position der Liste neue Elemente hinzugefügt, gelöscht oder ersetzt werden. Über einen Index kann somit jederzeit auf jedes beliebige Element gezielt zugegriffen werden.
Bei einem Set verhält es sich gegensätzlich. Es existiert schlichtweg keine konkrete Reihenfolge, auf der Einfluss genommen werden kann. Aus diesem Grund gibt es auch keine Möglichkeit, über einen Index auf ein Element zuzugreifen.

Während in eine List beliebige Elemente eingefügt werden können und keinerlei Prüfung bei hinzufügen von Elementen besteht, lässt ein Set dagegen jedes Element nur einmal zu. Wird versucht ein neues Objekt in das Set aufzunehmen, welches allerdings mit einem bereits bestehenden Objekt übereinstimmt, so  äußert sich dies, je nach Implementierung, entweder mit einer Exception oder aber das Hinzufügen wird stillschweigend ignoriert.

Dadurch, dass beispielsweise ein HashSet im wesentlichen auf einer HashMap basiert, wird ein Duplikat anhand der Hash-Repräsentation erkannt. Damit dies funktioniert, muss sichergestellt werden, dass entsprechend der Funktionalität die Methode hashCode() für jeden Datentyp, welches in ein HashSet aufgenommen wird, überschrieben wird:

{% highlight java %}
Set<User> superUser = new HashSet<User>();
User bruce = new User("Bruce");
User bruce2 = new User("Bruce");
User bruce3 = new User("Bruce");
superUser.add(bruce);
superUser.add(bruce2);
superUser.add(bruce3);
System.out.println("Total: " + superUser.size()); // Total: 1
{% endhighlight %}

Ebenso findet - je nach Implementierung - die Duplikatsprüfung im Falle von Hash-Kollisionen auch auf Basis der Methode equals() statt.
Unabhängig der Art, wie die Prüfung stattfindet, empfiehlt es sich vorweg unveränderliche (immutable) Datentypen in ein Set aufzunehmen, um unkontrollierbare Seiteneffekte bei der Duplikatsprüfung bei nachträglicher Objektveränderung zu vermeiden.

Das SortedSet ist im Gegensatz zur List und Set weit seltener im Einsatz. Dieser besondere Set-Typ besitzt dabei im Gegensatz zum normalen Set eine Reihenfolge. Allerdings hat der Entwickler keinen konkreten Einfluss auf die Reihenfolge, wie dies bei einem List-Objekt mithilfe eines Index möglich ist. Die Reihenfolge der Elemente wird einzig allein vom Wert, der im SortedSet befindlichen Objekte, bestimmt. Zur Festlegung der Reihenfolge wird dabei das Comparable-Interface herangezogen. Jedes Element, welches hinzugefügt werden soll, muss demzufolge das Comparable-Interface implementiert haben. Ist dies nicht der Fall, wird auch dieser Vorgang mit einer Exception abgebrochen. 

### Das List-Interface
Die drei wesentlichen Klassen, die das List-Interface implementieren sind ArrayList, Vector und LinkedList. 
Letztere Klasse findet dabei zu Unrecht eine geringe Aufmerksamkeit unter den Entwicklern. Gerade jedoch, wenn ein häufiges einfügen oder entfernen von Elementen im mittleren Teil der Liste stattfindet und dabei kein Zugriff auf ein Element über einen Index notwendig ist, bietet diese List-Implementierung eine bessere Performance als eine ArrayList. Findet kein häufiges hinzufügen oder löschen von Elementen statt, sondern werden hauptsächlich neue Elemente an das Ende der Liste hinzugefügt, so liefert eine ArrayList eine bessere Performance als eine LinkedList. 
Eine LinkedList basiert dabei im Kern im Gegensatz zu einer ArrayList nicht auf einen Array, der sämtliche Objekt-Referenzen aufnimmt. Stattdessen besteht dieser Datentyp aus Knoten, die jeweils neben dem eigentlichen Objekt einen „Zeiger“ auf den davor bzw. danach liegenden Knoten besitzen. Eine LinkedList lässt sich somit vereinfacht wie eine verkettete Aneinanderreihung von Elementen vorstellen. Jedes Element kennt dabei nur sein Vorgänger bzw. sein Nachfolger- Element. Da eine LinkedList nicht intern synchronisiert ist, muss bei parallelem Zugriff eine Synchronisierung stattfinden. Über die Möglichkeiten zum sicheren parallelisierten Zugriff erfahren Sie gleich mehr.

Die zwei bekannteren Klassen, die ebenso wie die LinkedList das List-Interface implementieren, sind ArrayList und Vector. Beide Datentypen sind sich somit aufgrund des gemeinsamen Interface weitgehend ähnlich. Der wesentliche Unterschied besteht jedoch im Synchronisierungszustand. Eine Instanz vom Typ Vector kümmert sich bereits intern um die Synchronisierung. Dies ist bei der ArrayList nicht der Fall.
Greifen somit mehrere Threads auf eine ArrayList-Instanz zu und verändern die interne Struktur durch hinzufügen oder löschen eines Elements ohne diesen Zugriff durch einen Synchronisierungsblock (synchronized) zu schützen, so besteht die Gefahr, dass die strukturelle Änderung mit einer ConcurrentModificationException abbricht.
Eine ArrayList kann jedoch durch eine „manuelle“ Kapselung ebenso in einen sicheren Synchronisierungsstand gebracht werden. Werden alle Zugriffe die Einfluss auf die Struktur nehmen beispielsweise in einem eigenen Datentyp vorgenommen („extern“ synchronisiert),  so kann auch dieser Datentyp sicher mit parallelisierten Zugriffen umgehen.

Aber auch die Klasse Collections (nicht mit dem Interface Collection zu verwechseln) bietet hierfür bereits einen generischen Ansatz, die anfallende Synchronisierungsarbeit zu bewältigen:

{% highlight java %}
ArrayList<String> stringList = new ArrayList<String>();
List synchronizedStringList = Collections.synchronizedList(stringList);
{% endhighlight %}

Die statische Methode synchronizedList (ebenso verfügbar für die Typen Map, Set, SortedMap und SortedSet) stellt eine synchronisierte Wrapper-Funktionalität durch Rückgabe eines SynchronizedList-Objekt zur Verfügung.
Doch auch diese Methodik ist kein Wundermittel und schützt lediglich bei atomaren Operationen (z.b. add(object)).
Das hat zur Folge, dass die interne Synchronisierung lediglich bei einen unabhängigen Zugriff wirksam ist.
Erfolgen aber mehrere aufeinanderfolgende Zugriffe, die voneinander abhängig sind, so muss trotzdem eine manuelle Synchronisierung erfolgen.

Notwendig ist dies beispielsweise bei der Iteration einer Liste, die von mehreren Threads verwendet wird: 

{% highlight java %}
public void processList() {
 List synchronizedlist = Collections.synchronizedList(new ArrayList());
  synchronized(list) {
      Iterator i = list.iterator();
      while (i.hasNext())
          processElement(i.next());
 }
}
{% endhighlight %}

Ebenso wird die Notwendigkeit der externen Synchronisierung bei der Klasse HashTable ersichtlich, bei der alle Methoden intern synchronisiert sind. Das mag verwirrend klingen, wieso ist eine externe, manuelle Synchronisierung notwendig, wenn bereits alle Methoden in der Klasse synchronisiert sind?
Folgendes Beispiel veranschaulicht die Notwendigkeit:

{% highlight java %}
public CacheEntry addToCache(HashTable table, String key, CacheEntry value) {
  synchronized(table) {
    if(table.containsKey(key)) {
      return table.get(key);
    } else {
      table.put(key, value);
      return value;
    }
  }
}
{% endhighlight %}

Wäre der synchronized-Block nicht vorhanden, bestünde die Gefahr einer Race Condition mit parallelen Zugriffen auf das HashTable-Objekt.
Schließlich kann nach Prüfung auf ein Element mit containsKey ,von einem weiteren Thread das Element entfernt werden, noch bevor mittels get das Element in Zeile 4 ermittelt wird.

Rückblickend gibt es also drei wichtige Datentypen in Verbindung mit dem List-Interface. Spielen konkurrierende Zugriffe eine Rolle, so kann die Synchronisierung entweder manuell erfolgen, die „altgebackene“ Klasse Vector (seit JDK 1) verwendet werden oder aber auch eine „sichere“ Liste mit der Methode synchronizedList erstellt werden. Unabhängig wie die Synchronisierung im Detail erfolgt, so geht jede Synchronisierung der Liste auf Kosten der Performance. Die Klasse Vector wird von vielen Entwicklern als altgebacken angesehen, was nicht verwunderlich ist, wenn man bedenkt, dass die Klasse seit Anbeginn von Java vorhanden ist.  Zudem kommt, dass die Klasse im technischen Vergleich gleichartiger Klassen nicht länger „State oft the Art“ ist. Statt die Klasse Vector einzusetzen, werden deshalb des Öfteren eigene Synchronisierungsimplementierungen auf Basis einer ArrayList vorgezogen. Dies ermöglicht vor allem effizientere Synchronisierungsblöcke zu implementieren, die je nach Programmlogik auf die entsprechenden Operationen zugeschnitten sind.
Nichtsdestotrotz kann auch die ArrayList als eigentlich unsychronisierte Liste im parallelisierten Applikationen eingesetzt werden, sofern lediglich lesende Zugriff stattfinden.

### Das Set-Interface
Unter den beliebtesten implementierenden Klassen des Set-Interface fallen HashSet, TreeSet, LinkedHashSet und EnumSet.
Am gängigsten ist die Klasse HashSet, die dabei intern hauptsächlich auf einer HashMap basiert und somit schnelle Lesezugriffe ermöglicht. Diese ist dabei jedoch in einem unsynchronisierten Zustand, weshalb auch hier entweder eine manuelle Synchronisierung erforderlich ist, oder auf die statische Methode synchronizedSet der Klasse Collections zurückgegriffen werden kann.

Gleiches gilt für die Klasse TreeSet. Diese Implementierung hält stets alle Elemente in ihrer natürlichen Reihenfolge indem die compareTo Methode des Comparable-Interface aufgerufen und der Rückgabewert ausgewertet wird. Alternativ kann eine individuelle Sortierreihenfolge mit Übergabe eines Comparator im Konstruktor festgelegt werden. Die Elemente des TreeSet befinden somit immer in einer sortierten Reihenfolge. Wird ein neues Element hinzugefügt, wird unmittelbar das Element an der entsprechenden Position eingeordnet. Das Vorgehen der Sortierung verhält sich damit im Prinzip ähnlich wie es der Aufruf der Methode sort der Klasse Collections für List-Objekte bewirkt. Ausgehend von einem List-Objekt (z.B. ArrayList) erfolgt entweder die Sortierung anhand der natürlichen Wertigkeit des Objektes (dies setzt die Implementierung des Comparable Interface vorrausetzt) oder unter Angabe eines Comparator.
 
Wie wir bereits einleitend festgehalten haben, existiert in jedem Set jedes Element nur ein einziges Mal.
Die Prüfung ob ein Element bereits existiert, erfolgt je nach Implementierung unterschiedlich. Während die Klasse HashSet anhand der Methode hashCode auf Übereinstimmung prüft, erfolgt die Prüfung bei einem TreeSet anhand des Resultats aus der Methode compareTo bzw. bei Verwendung des Comparator-Konstruktor der Klasse TreeSet anhand der Methode compare.
Ebenso wie die Klasse HashSet ist auch die Klasse TreeSet nicht intern synchronisiert und erfordert somit bei Bedarf eine zusätzliche Behandlung.

Die Klasse LinkedHashSet kombiniert im Wesentlichen die Klassen HashSet und LinkedList. Die Unterscheidung zu HashSet besteht in der konsistenten Reihenfolge der eingefügten Elemente. Während ein HashSet keine geordnete Reihenfolge seiner Elemente kennt, bleibt die Elemente eines LinkedHashSet in der Reihenfolge, in der diese ursprünglich eingefügt worden sind. 

Als letzte wesentliche implementierende Klasse von Set befindet sich mit EnumSet eine spezielle Implementierung von Set für Aufzählungstypen. Diese befindet sich im Java-Sprachumfang seit Version 5. Anfangs verwirrend mag das Fehlen eines öffentlichen Konstruktors sein. Stattdessen bietet die Klasse statische „Factory“-Methoden an, über die ein Set an Enumerations erstellt werden kann.
Die interne Abbildung der Aufzählungstypen ist dabei zugunsten einer hohen Performance ziemlich trickreich.

Rückblickend gibt es vier wesentliche Set implementierende Klassen (HashSet, LinkedHashSet, TreeSet, EnumSet). Alle diese Klassen sind dabei nicht intern synchronisiert. Spielen konkurrierende Zugriffe somit eine Rolle, so kann auch hierbei die Synchronisierung entweder manuell erfolgen oder eine „sichere“ Liste mit der Methode synchronizedSet erstellt werden. Im Gegensatz zu TreeSet und EnumSet erlauben die Typen HashSet und LinkedHashSet null als Wert.
Im Umgang mit Enumerations sollte immer auf den speziellen Set-Typ EnumSet zurückgegriffen werden. Andernfalls ist ein HashSet für die meisten Anwendungsfälle meiste die ideale Wahl.
