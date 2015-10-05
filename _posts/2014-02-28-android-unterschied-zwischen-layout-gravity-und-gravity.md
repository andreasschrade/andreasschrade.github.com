---
layout: post
title: "Android: Unterschied zwischen gravity und layout_gravity"
category: "android"
---



## Hintergrund

Mit den Attributen gravity und layout\_gravity lässt sich die Ausrichtung von View-Komponenten innerhalb (größerer) View-Komponenten vornehmen.
Beispielsweise lässt sich unter Angabe von "CENTER\_HORIZONTAL" das Objekt horizontal mittig innerhalb dessen Container ausrichten.
Weitere Optionene wären etwa "CENTER", "BOTTOM", "TOP".

### Die Unterscheidung

Zu unterscheiden gibt es folgende Attribute:

1. android:gravity
2. android:layout_gravity


<strong>1. android:gravity Attribut</strong>

Das gravity-Attribut ist Bestandteil einer View Group und gibt an, wie sich der Inhalt - also die Childs - der View Group horizontal und vertikal ausrichten.

<strong>2. android:layout_gravity Attribut</strong>

Das layout_gravity Attribut gibt die Ausrichtung eines Objektes im Verhältnis zu anderen Objekten an, die sich im gleichen Container befinden.
Beispielsweise lässt sich damit ausdrücken, dass innerhalb eines Containers ein Element links von einem weiteren Element ausrichten lässt.

## Beispiele
Im folgenden Screenshot wird ein LinearLayout mit einer horizontalen Ausrichtung verwendet.
Das LinearLayout hat die gravity-Angabe "center".

Dies bedeutet, dass alle darin enthaltenen Child-Views möglichst zentriert und in Reihenfolge der Deklration im XML dargestellt werden.

<img style="display:block; " src="{{ site.url }}/assets/2014-02-28-example-horizontal-no-layout-gravity.png">

Wird nun für alle drei TextViews die Layout-Angabe (siehe folgenden Code-Abschnitt) hinterlegt, lässt sich deren Ausrichtung festlegen:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:layout_weight="1"
    android:background="#EEEEEE"
    android:gravity="center"
    android:orientation="horizontal" >

    <TextView
        android:id="@+id/T1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="left"
        android:text="left" >
    </TextView>

    <TextView
        android:id="@+id/T2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="center" >
    </TextView>

    <TextView
        android:id="@+id/T3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="right"
        android:text="right" >
    </TextView>

</LinearLayout>
{% endhighlight %}

Die jeweilige Angabe von layout_gravity resultiert in folgendem Screenshot:

<img style="display:block; " src="{{ site.url }}/assets/2014-02-28-example-horizontal-gravity-center.png">

Dadurch, dass es sich um LinearLayout mit horizontaler Ausrichtung handelt, werden die Elemente horizontal zentriert dargestellt.
Unter Angabe von layout_gravity lässt sich nun die verbleibende Achse (Y-Achse) eines jeden Elements definieren.

Aus diesem Grund bietet sich für ein horizontales Layout folgende Eigenschaften für deren Childs an:

- top
- center
- bottom 


Im folgenden Screenshot wurde das LinearLayout zu einer vertikalen Ausrichtung geändert und die layout_gravity der einzelnen View-Childs gemäß deren Beschriftung angepasst:

<img style="display:block; " src="{{ site.url }}/assets/2014-02-28-example-vertical-gravity-center.png">

Auch in diesem Fall verhält es sich analog dem ersten Beispiel: Die Elemente werden vertikal mittig zentriert ausgerichtet und die verbleibende Achse (X-Achse) lässt sich mit folgenden sinnhaften Attributen definieren:

- left
- center
- right
