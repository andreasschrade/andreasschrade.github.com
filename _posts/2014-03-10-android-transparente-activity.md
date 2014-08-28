---
layout: post
title: "Android: Transparente Activity erzeugen"
---



## Hintergrund

Gelegentlich wird von Android-Entwicklern danach gefragt, wie sich eine transparente Activity erstellen lässt.
Im folgenden Abschnitt wird eine denkbar einfache und funktionierende Möglichkeit vorgestellt.

### Die Implementierung

Zur Implementierung einer transparenten Activity sind folgende beiden Schritte notwendig

1. Eigenen Theme unter "res/values/styles.xml" anlegen
2. Das Theme für die betroffenen Activities im Manifest anwenden


<strong>1. Anlage des Themes</strong>

Zuerst gilt es folgend abgebildete Style-Definition in der styles.xml unter "res/values" abzulegen:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<style name="Theme.Transparent" parent="android:Theme">
  <item name="android:windowIsTranslucent">true</item>
  <item name="android:windowBackground">@android:color/transparent</item>
  <item name="android:windowContentOverlay">@null</item>
  <item name="android:windowNoTitle">true</item>
  <item name="android:windowIsFloating">true</item>
  <item name="android:backgroundDimEnabled">false</item>
</style>
{% endhighlight %}

Unter älteren Android-Versionen existiert die Konstante "color/transparent" nicht. In diesem Fall kann die Konstante manuell unter "res/values/colors.xml" mit dem Wert "#00000000" (ARGB) hinterlegt werden.

<strong>2. Style anwenden</strong>

Der zuvor erzeugte Style lässt sich nun für beliebig viele Activities anwenden.
Hierzu muss lediglich das theme-Attribut der betroffenen Activity im Manifest ergänzt werden:

{% highlight xml %}
<activity android:name=".ExampleActivity" android:theme="@style/Theme.Transparent" />
{% endhighlight %}

Im folgenden Screenshot ist eine transparente Activity mit lediglich einer TextView dargestellt:
<img style="display:block; " src="{{ site.url }}/assets/2014-03-10-transparente-activity.png">
