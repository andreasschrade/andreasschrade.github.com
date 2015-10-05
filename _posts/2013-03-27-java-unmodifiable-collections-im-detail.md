---
layout: post
title: "Java: Unmodifiable Collections im Detail"
category: "java"
---



## Hintergrund
Gegenstand dieses Artikel stellen sogennante unmodifiable Collections dar.
Was sich dahinter verbirgt, erfahren Sie im folgenden Abschnitt:

### Einführung
Als Basis dieses Artikels soll erneut die Währungsliste, die auch bereits in vorhergehenden Artikeln verwendet wurde, eingesetzt werden:

{% highlight java %}
List<String> currencies = new ArrayList<String>();
currencies.add("Euro");
currencies.add("Dollar");
currencies.add("Pfund");
currencies.add("Yen");
{% endhighlight %}

Da in diesem Fall sichergestellt ist, dass sich der Umfang der Liste nicht nachträglich ändert, ist es immer ratsam, die Liste im Sinne einer guten Software-Architektur als unveränderlich zu definieren.
 
Dies geschieht am einfachsten über die Methode <em>unmodifiableList</em>:

{% highlight java %}
List<String> currencies = Collections.unmodifiableList(currencies);
{% endhighlight %}

Die Absicht der Methode ist die Erzeugung einer unveränderlichen Liste. Im Detail wird ein neues Objekt vom Typ <em>UnmodifiableList</em> erzeugt, welches dieselben Objekt-Referenzen erhält, die auch das Original List-Objekt zur Laufzeit der Erstellung trägt.

Der wesentliche Unterschied zu einer veränderlichen Liste ist, dass nachträglich der unveränderlichen Collection, keine Elemente hinzugefügt oder gelöscht werden können. Ein etwaiger Versuch, eine derartige Operation auszuführen, endet schonungslos in einer <em>UnsupportedOperationException</em>.

Keinen Einfluss hat dabei eine unveränderliche Liste auf die eigentlichen Objekte die diese enthält. Diese können nach wie vor verändert werden, sofern es sich bei diesen Elementen nicht um immutable Datentypen (z. B. String) handelt:

{% highlight java %}
List<User> user = new ArrayList<User>();
user.add(new User("Paul"));
	
List<User> unmodifiable = Collections.unmodifiableList(user);
unmodifiable.get(0).setName("Dirk"); // Veränderung auf Element der Liste
unmodifiable.add(new User("Hans")); //UnsupportedOperationException
unmodifiable.remove(0);//UnsupportedOperationException
{% endhighlight %}

Zusammengefasst handelt es sich bei der unveränderlichen Liste lediglich um eine spezielle Leseansicht, welches die gleichen Element-Referenzen wie das des Originals teilt und die enthaltenen Objekte selbst nach wie vor verändert werden können, sofern diese selbst nicht unveränderlich sind.

Ist der Rückgabewert einer Methode eine derartige unveränderliche Liste, sollte dies zur Vermeidung vor Verwirrung und Fehlern, im Javadoc (@return) der Methode vermerkt werden.

Dadurch, dass es sich bei dem Datentypen <em>UnmodifiableList</em> lediglich intern um einen Wrapper handelt, gibt es keine wesentlichen Unterschiede zwischen einer normalen und einer unveränderlichen Liste im Bezug auf die Performance. Der Overhead beschränkt sich auf die Weiterleitungsmethoden der Wrapper-Klasse, die allerdings durch Optimierungen seitens des Just-In-Time Compiler gering ausfällt.

Eine unveränderliche Liste ist daher meisten rein aus dem Gesichtspunkt einer saubereren Architektur bzw. einem besseren Entwicklungsstil zu betrachten.

Außerdem ist deren Einsatz teilweise essentiell, wenn eine Programmfunktionalität über eine API bereitgestellt wird, dessen Verwender nur auf eine eingeschränkte Auswahl an Elemente zugreifen darf.
Gerade bei diesem Einsatzgebiet sollte bedacht werden, dass die unveränderliche Liste, der Methode unmodifiableList zwar selbst unveränderlich ist, aber das List-Objekt auf dessen Grundlage die eingeschränkte Liste erzeugt wird, nach wie vor veränderlich ist. Das bedeutet, dass über die ursprüngliche Referenz der veränderlichen Liste Einfluss auf die gekapselte, eingeschränkte Liste genommen werden kann:

{% highlight java %}
List<String> modifiable = new ArrayList<String>();
List<String> unmodifiable = Collections.unmodifiableList(modifiable);

modifiable.add("EURO");
System.out.println(unmodifiable.size()); // 1
{% endhighlight %}

Die scheinbar unveränderliche Liste ist somit nur solange „geschützt“, bis eine Änderung der Struktur über die Referenz der ursprünglichen, veränderlichen Liste vorgenommen wird. 

