---
layout: post
title: "Hibernate – Sortierung mit Hibernate (order by)"
---



## Implementierung
Sortierung innerhalb von Hibernate lässt sich am einfachsten über das Criteria-Objekt abbilden.

Konkret lässt sich eine Sortierung mit Aufruf von <em>addOrder</em> und Angabe der Spaltenbezeichnung realisieren.
Folgendes Snippet verwendet dabei in einem Query sogar zwei Sortierungen. Die Sortierung nach "name" erfolgt aufsteigend, während die Sortierung nach "age" in absteigender Reihenfolge herangezogen wird, falls sich zwei Objekte den gleichen Namen teilen.

{% highlight java %}
List birds = sess.createCriteria(Bird.class)
    .addOrder( Order.asc("name") )
    .addOrder( Order.desc("age") )
    .list();
{% endhighlight %}
