---
layout: post
title: "Android: Service beim Bootvorgang starten"
category: "android"
---



## Hintergrund

Wird für eine App eine permanente Verarbeitung im Hintergrund erfordert, so wird hierfür meist ein Service verwendet.
Dieser kann bei Bedarf - beispielsweise in Interaktion mit dem Benutzer - gestartet werden, oder soll unabhängig von Benutzeraktivitäten
permanent im Hintergrund laufen.
Dies erfordert das Einklinken des Service-Starts in den Boot-Prozess:

### Integration des Services in den Boot-Prozess

Zur Integration sind folgende Schritte notwendig:

1. Permission "RECEIVE\_BOOT\_COMPLETED" im Manifest ergänzen
2. Individuelle Implementierung eines BroadcastReceiver
3. BroadcastReceiver im Manifest registrieren

<strong>1. Permission ergänzen</strong>

Im Manifest der Applikation muss folgende Berechtigung eingefügt werden:

{% highlight xml %}
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
{% endhighlight %}

<strong>2. BroadcastReceiver implementieren</strong>

Der stattgefundene Boot-Prozess wird mittels dem Broadcast "BOOT_COMPLETED" signalisiert.
Hierzu wird ein separater BroadcastReceiver benötigt, der auf diesen Broadcast lauscht und den Start des Services vornimmt:

{% highlight java %}
public class OnBootBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Intent startServiceIntent = new Intent(context, ExampleService.class);
        context.startService(startServiceIntent);
    }
}
{% endhighlight %}

<strong>3. BroadcastReceiver registrieren</strong>

Zuletzt muss der zuvor erzeugte Receiver im Manifest registriert werden.
Der full-qualified Package-Name muss entsprechend ergänzt werden. Etwaige Attribute wie <em>enabled</em> oder <em>exported</em> müssen nicht gesetzt werden.
Die Default-Werte sind in diesem Fall bereits korrekt.

{% highlight xml %}
<receiver android:name="de.example.BootBroadcastReceiver">  
    <intent-filter>  
        <action android:name="android.intent.action.BOOT_COMPLETED" />  
    </intent-filter>  
</receiver>
{% endhighlight %}


<div class="message">
  <strong>Wichtig!</strong>
  Der Aufruf des OnBootReceivers erfolgt nur, wenn die App sich auf dem internen Speicher befindet!
  Wurde die App auf der SD-Karte installiert, wird der Receiver nicht aufgerufen und somit auch der Service nicht gestartet.
  Es ist somit empfehlenswert die Installation auf SD-Karte vornherein zu unterbinden:
  Hierzu muss das Attribut <em>installLocation</em> auf den Wert "internalOnly" gesetzt werden:
</div>
  {% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
  package="de.example.test"
  android:versionCode="1"
  android:versionName="1.0" 
  android:installLocation="internalOnly">
  ...
{% endhighlight %}
