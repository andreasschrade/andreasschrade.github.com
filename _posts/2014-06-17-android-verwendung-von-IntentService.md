---
layout: post
title: "Android: Der IntentService im Detail"
---



## Hintergrund

Die Klasse <em>IntentService</em> ist eine besondere Ausprägung (Subtyp) von <em>Service</em>.
Der Intent-Service dient als eine Art Wrapper um Aufgaben vom Main-Thread der Applikation in einer Queue auszulagern, welche mittels einem Worker-Thread sequentiell bearbeitet wird.
Die vom Client mittels <em>startService(intent)</em> erfolgten Aufrufe werden in der Queue entsprechend deren Reihenfolge (FiFo) solange abgearbeitet, bis kein Intent mehr vorhanden ist. In diesem Fall beendet sich der Service automatisch.

### Implementierung

Um einen IntentService verwenden zu können, muss ein individueller Subtyp erstellt werden und die Methode <em>onHandleIntent(Intent)</em> implementiert werden.
Im Gegensatz zu einem "normalen" Service erfordert der IntentService keinerlei "Background-Handling"-Mechanismus, da dieser standardmäßig nicht im Main-Thread der Applikation ausgeführt wird.

Ein Intent-Service eignet sich somit vor allem dazu, um zyklische (vor allem langlaufende) Aufgaben on-Demand sequentiell gemäß deren initiierten Reihenfolge zu bearbeiten.

Folgend der Stub einer IntentService-Implementierung:
 
{% highlight java %}
public class MyIntentService extends IntentService {

  public MyIntentService(String name) {
    super(name);
  }

  @Override
  public void onCreate() {
    // TODO Aufgabe erledigen, die bei Erstellung des Services erledigt werden sollen.
    // z.B. System-Service beziehen, Context Instanzvariable zuweisen
  }
  
  @Override
  protected void onHandleIntent(Intent intent) {
    // TODO Zeitaufwendige Implementierung hinzufügen, die bei jeder Intent-Bearbeitung erfolgen muss.
  }
}

{% endhighlight %}

Der IntentService muss zudem noch wie gewöhnlich im Manifest registriert werden:

{% highlight xml %}
<service android:name="de.example.MyIntentService" />
{% endhighlight %}


Im Gegensatz zu einer <em>Service</em>-Implementierung, bei der mittels Aufruf von <em>stopSelf()</em> oder <em>stopService()</em> das Beenden des Services nach erledigter Arbeit eingeleitet wird, beendet sich der IntentService automatisch, wenn alle Requests bearbeitet wurden. Ein expliziter Aufruf von <em>stopSelf()</em> ist deshalb in diesem Fall nicht notwendig.

Der IntentService ist gerade auch aufgrund der einfachen und schnellen Implementierung eine wirklich gelungene Ergänzung zu <em>AsyncTask</em> und <em>Service</em>.
