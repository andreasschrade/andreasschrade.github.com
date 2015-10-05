---
layout: post
title: "Android: Prüfung ob Internetverbindung besteht"
category: "android"
---



## Hintergrund

In nahezu allen Apps, die Ressourcen über das Internet beziehen oder bereitstellen, kann eine Prüfung vorteilhaft sein, ob überhaupt eine Internetverbindung besteht.
Sicherlich lässt sich auch schlichtweg versuchen, eine Ressource abzufragen und im Fehlerfall, wenn keine Verbindung besteht, den quittierten Timeout entsprechend zu interpretieren.
Der Nachteil bei diesem Vorgehen besteht allerdings darin, dass es eben erst zu dem besagten Timeout kommen muss welches wiederum entsprechend Zeit benötigt.

### Die Implementierung

Die Implementierung der Prüfung ist denkbar einfach: Hilfreich erweist sich hierzu die Verwendung der Klasse <em>ConnectivityManager</em> bzw. deren Methode <em>getActiveNetworkInfo</em>.
Die Methode liefert den aktiven Default-Verbindungstyp als ein Objekt vom Typ <em>NetworkInfo</em>.
Mit Aufruf von <em>isConnectedOrConnecting</em> lässt sich schließlich abfragen, ob dieser Verbindungstyp aktiviert ist:

{% highlight java %}
public boolean isOnlineConnectivityAvailable() {
    ConnectivityManager cm = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
    NetworkInfo netInfo = cm.getActiveNetworkInfo();
    return (netInfo != null && netInfo.isConnectedOrConnecting()) {
}
{% endhighlight %}

Ist der Flugzeugmodus aktiviert, wird als NetzworkInfo <em>null</em> zurückgegben, weshalb die Prüfung entsprechend notwendig ist.

Zuletzt wird noch eine Berechtigung für die Abfrage benötigt, die im Manifest wie gewohnt hinterlegt werden muss:
 {% highlight xml %}
 <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
 {% endhighlight %}