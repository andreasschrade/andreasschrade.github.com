---
layout: post
title: Android: Individuelle Notifications erstellen
---




Notifications in Android eignen sich ideal geradezu für Service oder sonstige etwaige Applikationen, die vorübergehend im Hintergrund laufen, aber dennoch den Benutzer auf eingetretene Ereignisse informieren wollen. Neben der Einblendung eines einfachen Icons und dem optional dazugehörigen Ticker-Text besteht auch die Möglichkeit die aufgeklappte erweiterte Ansicht mit einem individuell gestalteten Layout entsprechend den Anforderungen zu entwickeln.

Folgende Schritte sind hierfür notwendig:

{% highlight java %}
NotificationManager notificationManager = (NotificationManager)getSystemService(Context.NOTIFICATION_SERVICE);
{% endhighlight %}

Der NotificationManager ist ein Systemservice in Android, der für das Handling der Notification benötigt wird.
Die entsprechende Referenz wird durch Aufruf der getSystemService-Methode ermöglicht die als Argument die ID des Notification-Service erwartet.

{% highlight java %}
int icon = R.drawable.stat;
String tickerMessage = "Hello Notification";
long displayTime = System.currentTimeMillis();
 
Notification notification = new Notification(icon, tickerMessage, displayTime);
notification.flags = notification.flags | Notification.FLAG_ONGOING_EVENT;
{% endhighlight %}

Desweiteren werden weitere Attribute zur Anzeige der individuellen Notification benötigt.
Dazu gehört das Icon (Drawable) welches angezeigt werden soll, die Ticker-Nachricht die einmalig bei der Einblendung angezeigt wird, sowie schließlich ein aktueller Timestamp.
Letzteres ist notwendig, da die Anordnung der Elemente in der erweiterten (ausgeklappten) Status-Bar sich anhand der Anzeigezeit richtet bzw. diese anhand deren sortiert.
Anhand dieser Informationen kann nun ein neues Notification-Objekt erzeugt werden, welches in der darauf folgenden Zeile das Flag “ONGOING_EVENT” erhält.

Dieses Flag, sagt aus, dass die Nachricht in der erweiterten Statusleiste unter die Rubrik “Laufend” fällt. Was konkret bedeutet, dass diese Nachricht permanent angezeigt wird, da es sich in der Regel um einen laufenden Hintergrundprozess handelt der dadurch signalisiert wird.
Dieses Flag ist beispielsweise für Anwendungen interessiert, die permanent den Benutzer über die Hintergrundaktivität der App informieren möchte. Beispielsweise eine App die permament den Ladezustand des Akkus anzeigt.

Der Eintrag der Nachricht in der ausgeklappten Statusleiste lässt sich indivduell gestalten.
Ermöglicht wird dies durch ein RemoteView-Objekt:

{% highlight java %}
RemoteViews contentView = new RemoteViews(getPackageName(), R.layout.notification);
 contentView.setImageViewResource(R.id.image, R.drawable.icon);
 contentView.setTextViewText(R.id.title, "Extended Title");
 contentView.setTextViewText(R.id.text, "Description ...");
 notification.contentView = contentView;
{% endhighlight %}

{% highlight xml %}
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:id="@+id/layout"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   android:padding="10dp" >
    <ImageView android:id="@+id/image"
       android:layout_width="wrap_content"
       android:layout_height="fill_parent"
       android:layout_alignParentLeft="true"
       android:layout_marginRight="10dp" />
    <TextView android:id="@+id/title"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:layout_toRightOf="@id/image"/>
    <TextView android:id="@+id/text"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:layout_toRightOf="@id/image"
       android:layout_below="@id/title" />
</RelativeLayout>
{% endhighlight %}

RemoteView Objekte erlauben die Steuerung eines Layouts innerhalb einer separaten Applikation.
In diesem Fall wird das Layout (R.drawable.notification) in der erweiterten ausgeklappten Statusleiste angezeigt.
Wichtig bei setzen eines individuellen ContentView Objektes ist, dass auch eine zugehöriges contentIntent gesetzt wird!

Dies geschieht dabei wie folgt:

{% highlight java %}
Intent intent = new Intent(this, main.class);
PendingIntent pendingIntent = PendingIntent.getActivity(getBaseContext(), 0, intent, 0);
notification.contentIntent = pendingIntent;
{% endhighlight %}

Das erzeugte PendingIntent wird ausgeführt, wenn der Benutzer auf den Eintrag in der aufgeklappten Statusbar auswählt.
In diesem Beispiel zeigt das PendingIntent auf die eigene Applikation bzw. auf die ursprüngliche Activity, die somit wieder aufgerufen wird, bei Auswahl des Notification Eintrages.

Die Notification ist mit diesen Schritten vervollständigt und kann nun bei Bedarf angezeigt werden:

{% highlight java %}
int notificationId = 1;
notificationManager.notify(notificationId, notification);
{% endhighlight %}

Um eine Notificatin Applikationsweit wieder zu erkennen bzw. um ggf. diese auszublenden oder erneut einzublenden, wird der Methode “notify” des NotificationManagers, die zur Anzeige der Notification dient, neben dem ursprünglichen Notification Objekt zusätzlich eine Ganzzahl übergeben.
Diese Ganzzahl dient als eindeutige ID der Benachrichtigung.
Soll beispielsweise die Notification bewusst wieder ausgeblendet werden, wird lediglich die Notification Referenz bzw. die ID benötigt

{% highlight java %}
notificationManager.cancel(notificationId);
{% endhighlight %}