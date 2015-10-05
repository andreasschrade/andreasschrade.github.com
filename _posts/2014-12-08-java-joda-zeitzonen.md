---
layout: post
title: "Joda API – DateTime mit Zeitzoneninformation erstellen"
category: "java"
---



## Implementierung
Grundsätzlich gilt, dass wenn mittels dem Default-Konstruktor ein DateTime-Objekt erzeugt wird, die Systemzeitzone des Benutzers verwendet wird.

Die Klasse DateTimeZone repräsentiert unter Joda eine Zeitzone. Gilt es nun eine individuelle, vom default abweichende Zeitzone zu setzen, lässt sich ein Objekt vom Typ DateTimeZone über folgende Wege erstellen:

{% highlight java %}
DateTimeZone dtz1 = DateTimeZone.forOffsetHours(2); // UTC+02:00
DateTimeZone dtz2 = DateTimeZone.forOffsetHoursMinutes(2,30); // UTC+02:30
DateTimeZone dtz3 = DateTimeZone.forID(„Europe/Berlin“); // UTC+01:00
{% endhighlight %}

Ein DateTime objekt lässt sich nun unter Berücksichtigung der Zeitzoneninformationen wie folgt erstellen:

{% highlight java %}
DateTime now = new DateTime(DateTimeZone.forOffsetHours(1));
{% endhighlight %}

Wichtig im Umgang mit Zeitzonen ist die Berücksichtigung der Sommer- und Winterzeit (DST Daylight Saving Time).