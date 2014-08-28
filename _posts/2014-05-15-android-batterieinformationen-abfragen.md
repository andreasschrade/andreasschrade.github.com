---
layout: post
title: "Android: Akkustatus abfragen"
---



## Hintergrund

Mithilfe der Battery API unter Android lassen sich allerlei Batterie-Informationen abfragen. Darunter befindet sich beispielsweise die Voltage, die Temperatur oder schlichtweg der Status.

Durch den <em>ACTION\_BATTERY\_CHANGED</em>-Broadcast, der bei jeder Veränderung gesendet wird, lässt sich ein entsprechender BroadcastReceiver implementieren, der bei Veränderung des Status die entsprechenden Informationen abfrägt und der eigenen Anwedung zur Verfügung stellt.
Die einzelnen Batterie-Informationen werden als Extra dem Broadcast-Intent beigefügt (z. B. EXTRA_HEALTH).
 
### Implementierung

Zuerst wird ein Broadcast-Receiver registriert, der auf einen Broadcast vom Typ <em>ACTION\_BATTERY\_CHANGED</em> lauscht:

{% highlight java %} 
registerReceiver(this.batteryInfoReceiver, new IntentFilter(Intent.ACTION_BATTERY_CHANGED));
{% endhighlight %}

Im Anschluss erfolgt die Implementierung des Broadcast-Receivers:

{% highlight java %} 
BroadcastReceiver batteryInfoReceiver= new BroadcastReceiver() {
 @Override
 public void onReceive(Context context, Intent intent) { 
  int health = intent.getIntExtra(BatteryManager.EXTRA_HEALTH,0);
  int level = intent.getIntExtra(BatteryManager.EXTRA_LEVEL,0);
  int plugged = intent.getIntExtra(BatteryManager.EXTRA_PLUGGED,0);
  boolean present = intent.getExtras().getBoolean(BatteryManager.EXTRA_PRESENT);
  int status = intent.getIntExtra(BatteryManager.EXTRA_STATUS,0);
  String technology = intent.getExtras().getString(BatteryManager.EXTRA_TECHNOLOGY);
  int temperature = intent.getIntExtra(BatteryManager.EXTRA_TEMPERATURE,0);
  int voltage= intent.getIntExtra(BatteryManager.EXTRA_VOLTAGE,0);
  }
}
{% endhighlight %}

