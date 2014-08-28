---
layout: post
title: "Android: Vibrationsalarm verwenden"
---



## Hintergrund

Der Vibrationsalarm eignet sich grundsätzlich, um nahezu lautlos den Besitzer des Smartphones auf ein Ereignis hinzuweisen.

### Implementierung

Die Vibrationsfunktion ist grundsätzlich mit nur wenigen Zeilen Code integriert und bietet dennoch einen hohen Grad an Flexibilität bezüglich dem Vibrationsmuster.

Zum einem lässt sich sowohl ein Vibrationsmuster definieren, als auch eine einmalige Zeitspanne definieren, in der vibriert werden soll.

In beiden Fällen jedoch wird die Berechtigung zur Vibration benötigt:
{% highlight xml %}
<uses-permission android:name="android.permission.VIBRATE"/>
{% endhighlight %}

<strong>Einmalige Vibration</strong>
Die einmalige Vibration eignet sich um ein haptisches Feedback abzubilden.
Dazu genügt es die zu vibrierende Zeitspanne in Millisekunden anzugeben:

{% highlight java %}
Vibrator v = (Vibrator) getSystemService(Context.VIBRATOR_SERVICE);
v.vibrate(300);  // Vibration für 300 Millisekunden
{% endhighlight %}

<strong>Vibrationsmuster</strong>
Zu Benachrichtigungszwecken eignet sich besonderns, ein Vibrationsmuster anzuwenden, welches den Benutzer auf ein Ereignis hinweist.

Hierbei lässt sich das Muster der Vibration in einem long-Array ausdrücken:

{% highlight java %}
long pattern[]={0,200,400,600,800};
{% endhighlight %}

Der erste long (0) definiert die Zeitspanne in Millisekunden bis zur Aktivierung der Vibration.
Alle folgenden Variablen definieren abwechselnd die Zeitspanne in Millisekunden der aktiven Vibration bzw. der dazwischenliegenden Pause.

Das abgebildete Pattern beschreibt dabei konkret folgendes Muster:
 - 0: Es findet keine Verzögerung der Vibration statt. 
 - 200: Die Vibration ist für 200 ms aktiv
 - 400: Die Vibration pausiert für 400 ms
 - 600: Die Vibration ist für 600 ms aktiv
 - 800: Die Vibration pausiert für 800 ms
 
Über ein weiteres Argument lässt sich nun definieren, ob dieses Muster wiederholt werden soll bzw. falls ja, welche Position der Startpunkt der Wiederholung darstellt.
Wird -1 angegeben, bedeutet dies, dass keine Wiederholung stattfinden soll.
Wird 0 angegeben, bedeutet dies, dass die Wiederholung ab der ersten Position erfolgen soll.
Wird 2 angegeben, bedeutet dies, dass die Wiederholung ab der dritten Position erfolgen soll.

{% highlight java %} 
vibrator.vibrate(pattern, modus); // modus == -1: Keine Wiederholung; modus == 0: Wiederholung ab Position 1 ... 
{% endhighlight %}
 
Um Zugriff auf die Vibrationsfunktion zu erhalten gilt es nun den zugehörigen System-Service zu beziehen:

{% highlight java %} 
long pattern[] = { 0, 100, 200, 300, 400 };
Vibrator vibrator = (Vibrator) getSystemService(Context.VIBRATOR_SERVICE);
vibrator.vibrate(pattern, 0);
{% endhighlight %}

Erfolgt eine Wiederholung des Vibrationsmusters, muss entsprechend der Abbruch der Vibration herbeigeführt werden.
Dies könnte klassischerweise das Annehmen eines Telefonats sein:

{% highlight java %} 
vibrator.cancel();
{% endhighlight %}

