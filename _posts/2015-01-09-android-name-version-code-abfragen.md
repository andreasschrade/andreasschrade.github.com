---
layout: post
title: "Android: Versionsname und Versioncode der App ermitteln"
category: "android"
---



## Hintergrund

Hin und wieder ist es erforderlich, in der App die aktuell betriebene Version abzufragen.
Dies kann beispielsweise notwendig sein, um Migrationen zwischen Versionen durchzuführen, oder beispielsweise auch aus Reportingzwecken.

### Realisierung

Grundsätzlich gibt es zwei Möglichkeiten, die Abfrage auf die Versionsinformationen durchzuführen.

Die erste, ältere Version ist grunsätzlich <em> nicht zu empfehlen <em>, hierbei wird über den Context, der PackageManager bezogen und anschließend die Package-Info auf Basis des Packagenames der App ermittelt.
Nachteilig bei dieser Methode ist, dass dies mittels try-catch gesichert werden muss und zudem ein Context-Objekt vorhanden sein muss.

{% highlight java %}
String versionName = context.getPackageManager().getPackageInfo(context.getPackageName(), 0).versionName;
{% endhighlight %}


<em>Besser ist es</em>, den Weg über die zur Build-Zeit generierten Klasse <em>BuildConfig</em> zu gehen.
Diese steht mit Verwendung des Android Studio in Version 0.7 zur Verfügung und stellt alle Informationen zur Version bereit:

{% highlight java %}
String versionName = BuildConfig.VERSION_NAME;
{% endhighlight %}

Neben Versionsname (z.B. 1.2.4) und Versionscode (z.B. 42), lässt sich darüber auch mittels der Konstante "APPLICATION_ID" das Package der App (z.B. de.example.myapp) beziehen.