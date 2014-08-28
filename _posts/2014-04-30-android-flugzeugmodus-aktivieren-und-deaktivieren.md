---
layout: post
title: "Android: Flugzeugmodus aktivieren/deaktivieren"
---



## Hintergrund

Der Flugzeugmodus beschreibt grundsätzlich einen Zustand des Smartphones, in welchem keinerlei Funkverbindungen (GSM, WLAN, NFC, Bluetooth) aktiv sind.
Alle drahtlosen Kommunikationsmöglichkeiten sind somit bei aktiviertem Flugzeugmodus deaktiviert.

Um in Flugzeugen Signal-Indifferenzen zu vermeiden, die in Verbindung mit dem Smartphone auftreten können, gilt es daher das Smartphone immer in den Flugzeugmodus zu versetzen.
Wie sich dieser Modus in Ihrer App verändern lässt, ist im folgenden Abschnitt beschrieben.

### Implementierung

<strong>Aktuellen Zustand abfragen </strong>

Da der Flugzeugmoduszustand lediglich "getoggled" werden kann, gilt es vorher den aktuellen Zustand abzufragen.

{% highlight java %}
boolean isAirplainModeEnabled = Settings.System.getInt(this.getContentResolver(), Settings.System.AIRPLANE_MODE_ON, 0); 
{% endhighlight %}

Durch den Aufruf von <em>getInt()</em> auf <em>Settings.System.AIRPLANE\_MODE\_ON</em> lässt sich mit Angabe des ContentResolver der aktuelle Zustand als Boolean abfragen.

<strong>Zustand verändern</strong>

Um den Zustand zu verändern, muss lediglich die Methode <em>getInt()</em> durch <em>putInt</em> im vorherigen Code-Snippet verändert werden:

{% highlight java %}
// Aktiviert den Flugzeugmodus
Settings.System.putInt(getContentResolver(),Settings.System.AIRPLANE_MODE_ON,1);

//Deaktiviert den Flugzeugmodus
Settings.System.putInt(getContentResolver(),Settings.System.AIRPLANE_MODE_ON,0);
{% endhighlight %}

Bei jeder Änderung des Flugzeugmodus gilt es den zugehörigen Broadcast (<em>ACTION\_AIRPLANE\_MODE\_CHANGED</em>) auszulösen, um die Änderung anzuwenden:

{% highlight java %}
Intent intent = new Intent(Intent.ACTION_AIRPLANE_MODE_CHANGED);	
intent.putExtra("state", state); // state == true, wenn der Modus aktiviert wurde, andernfalls false										
sendBroadcast(intent);
{% endhighlight %}

Zuguterletzt muss noch die Berechtigung zum Schreiben der Einstellung im Manifest hinterlegt werden:

{% highlight xml %}
<uses-permission android:name="android.permission.WRITE_SETTINGS" />
{% endhighlight %}