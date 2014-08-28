---
layout: post
title: "Android: Einfache Dialoge mithilfe von AlertDialog.Builder erzeugen"
---



## Hintergrund

Eine stellenweise Verlinkung (beispielsweise auf Webseiten) innerhalb von TextView-Elementen stellt eine gute Alternative zu dedizierten Button-Elementen dar.
Wie Verlinkungen innerhalb von TextViews auf Ressourcen vorgenommen werden können, wird im folgenden Abschnitt näher erläutert.

### Verlinkungen in TextView-Elementen

Grundsätzlich gibt es zwei Ansätze mit deren Hilfe "einfacher" Text innerhalb von TextViews verlinkt werden kann:

1. Die Klasse <em>LinkMovementMethod</em>
2. Das Attribut "autoLink"

<strong>1. Die Klasse LinkMovementMethod im Detail</strong>

Die Klasse <em>TextView</em> stellt mit der Methode <em>setMovementMethod</em> eine Möglichkeit dar, ein Objekt vom Typ <em>MovementMethod</em> zu setzen, welches wiederum um Einfluss auf den Cursor, dem Scrolling und der Text-Auswahl der TextView-Komponente ermöglicht.
In diesem konkreten Fall ist der Subtyp <em>LinkMovementMethod</em> überaus hilfreich. Der Einsatz der Klasse sorgt dafür, dass eine Verlinkung (z.B. via anchor-Tag innerhalb der angezeigten String-Ressource) entsteht und bei Berührung die verlinkte Ressource im Default-Browser geöffnet wird.

{% highlight java %}
public boolean isOnlineConnectivityAvailable() {
TextView tv = (TextView)findViewById(R.id.txtView);
tv.setMovementMethod(LinkMovementMethod.getInstance());
}
{% endhighlight %}

{% highlight xml %}
<string name="example">lorem ipsum <a href="http://google.de">google</a> bacon</string>
{% endhighlight %}

Der Einsatz von LinkMovementMethod erlaubt es, dass mit Berührung des Links ("google") die entsprechende Google-Startseite aufgerufen wird.


<strong>2. Das Attribut android:autoLink</strong>

Alternativ zur vorherig gezeigten Möglichkeit besteht eine deutlich einfachere Möglichkeit mit setzen von "android:autoLink=web".
Dies verlinkt automatisch jegliche als Web-Ressource erkannte Textpassage innerhalb des TextView-Elementes.

{% highlight xml %}
    <TextView
        android:id="@+id/txtView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:autoLink="web"
        android:text="@string/example">
    </TextView>
{% endhighlight %}

Der Nachteil besteht dieser scheinbar einfachen Methode steckt im Detail, denn lediglich eindeutig als Web-Ressource erkannte Text-Passagen werden verlinkt!
Obwohl die Textstelle eindeutig mittels anchor-Tag gekennzeichnet ist, klappt auf diesem Weg nur die Verlinkung, wenn der angezeigte Text selbst als Web-Ressource identifizierbar ist.

Beispiel:
Die im ersten Abschnitt angewendete Verlinkung "<a href="http://google.de">google</a>" würde beispielsweise unter Verwendung von autoLink nicht funktionieren, da der angezeigte Text (= google)
keine Internet-Ressource darstellt. Würde dagegen die Beschriftung auf "google.de" geändert werden, würde von nun an ebenso die Verlinkung via autoLink funktionieren.

<strong>Fazit</strong>
Grundsätzlich sollte die autoLink-Methode aufgrund der einfachen Anwendung bevorzugt werden, solange diese anwedbar ist:
Anwendbar ist diese, wenn alle Verlinkungen auch als eindeutige Internet-Resource dargestellt werden (= google.de) und kein umschreibender Text verwendet (= hier klicken) wird.

Ansonsten bietet die im ersten Abschnitt dargestellte Möglichkeit eine höhere Flexibilität.

Anzumerken ist zudem noch, dass es neben "web" zudem für autoLink die Eigenschaften "email", "map", "phone" und "all" gibt.

