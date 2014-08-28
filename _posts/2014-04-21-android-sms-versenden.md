---
layout: post
title: "Android: SMS versenden"
---



## Hintergrund

Zwar hat die SMS längt nicht mehr der Stellenwert als <em>das</em> mobile Kommunikationsmittel, dennoch gibt es vereinzelt Anwendungsfälle, bei der der Versand von SMS-Nachrichten erforderlich ist.
### Implementierung

Grundsätzlich gibt es zwei unterschiedliche Ansätze, wie eine SMS versendet werden kann:

 1. Versand über eine SMS-App
 2. Direkter Versand mittels SmsManager
 
<strong> 1. Versand über SMS-App</strong>

Beim Versand der SMS über die eingestellte SMS-App erhält der Benutzer nochmal die Möglichkeit zur Kontrolle.
Der Benutzer muss somit aktiv die vorausgefüllte SMS versenden:

{% highlight java %}
Intent intent = new Intent( Intent.ACTION_VIEW, Uri.parse("sms:" + phoneNumber));
intent.putExtra("sms_body", "Nachricht der SMS...");
startActivity(intent);
{% endhighlight %}

Vorteil bei dieser Variante ist, dass der Versand für den Benutzer transparent ist und die SMS im Gesprächsverlauf der SMS-Anwendung sichtbar ist.

<strong> 2. Direkter Versand mittels SmsManager</strong>

Im Gegensatz zur zuvor vorgestellten Methode ist hierbei keine Bestätigung des Benutzers zum Versand notwendig. Der Versand erfolgt somit für den Benutzer "unsichtbar".

{% highlight java %}
SmsManager sms = SmsManager.getDefault();
sms.sendTextMessage(number, null, message, null, null);
{% endhighlight %}

Zudem wird die Berechtigung <em>SEND_SMS</em> im Manifest benötigt:
{% highlight xml %}
<uses-permission android:name="android.permission.SEND_SMS"/>
{% endhighlight %}

Neben der Telefonnummer bzw. der SMS-Nachricht lässt sich Mithilfe der letzten beide Methodenparameter jeweils ein <em>PendingIntent</em> hinterlegen, welches ausgelöst werden soll wenn
 - die Nachricht erfolgreich versendet wurde
 - die Nachricht erfolgreich zugestellt wurde
 
