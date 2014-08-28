---
layout: post
title: "Android: Layouts mithilfe von <merge> und <include> wiederverwenden"
---



## Hintergrund

Seit der Unterstützung von Fragments ist es durchaus üblich, sowohl ein separates Layout für Smartphone-, als auch für Tablet-Besitzer anzubieten.
In diesem Zusammenhang spielt vor allem die Wiederverwendbarkeit bestehender Layout-Ressourcen eine große Rolle.
Wie sich die Wiederverwendbarkeit Mithilfe von <merge> und <include> realisieren lässt, ist im folgenden Abschnitt beschrieben. 

### Vorgehensweise

<strong> Das include-Tag </strong>
Das include-Tag ermöglicht die Integration von Layout-XML-Dateien in bestehende Layouts. Grundsätzlich verhält sich dies dabei, als ob das inkludierte Layout 1:1 in die zu importierende Layout-Ressource kopiert worden wäre:

Basis-Layout:

{% highlight xml %}
<FrameLayout>
   <include layout="@layout/layoutFragment"/>
</FrameLayout>
{% endhighlight %}

LayoutFragment-Layout:
{% highlight xml %}
<FrameLayout>
   <TextView android:id="@+id/txtView" android:layout_width="wrap_content" android:layout_height="wrap_content"/>
</FrameLayout>
{% endhighlight %}

Die Integration des Layouts würde somit wie folgt effektiv resultieren:
{% highlight xml %}
<FrameLayout>
   <FrameLayout>
      <TextView android:id="@+id/txtView" android:layout_width="wrap_content" android:layout_height="wrap_content"/>
   </FrameLayout>
</FrameLayout>
{% endhighlight %}

Das Beispiel verdeutlicht, dass wirklich die gesamte Layout-Ressource 1:1 kopiert wurde. Dies birgt die Unschönheit, dass das Root-Layout von <em>LayoutFragment</em> entsprechend übernommen wurde.
Eine unnötige Verschachtelung, die das Rendering des Layouts unnötigerweise verlangsamt.

Aus diesem Grund wurde im gleichen Zuge das merge-Tag eingeführt.

<strong> Das merge-Tag </strong>
Das merge-Tag wird nun - im Gegensatz zum include-Tag - in der Layout-Ressource eingesetzt, die von anderen Layouts "inkludiert" bzw. importiert werden soll.
Es fungiert als eine Art Root-Layout-Platzhalter, welches beim Merge-Prozess dynamisch entfernt wird.

Basis-Layout:

{% highlight xml %}
<FrameLayout>
   <include layout="@layout/layoutFragment"/>
</FrameLayout>
{% endhighlight %}

LayoutFragment-Layout:
{% highlight xml %
<merge>
   <TextView android:id="@+id/txtView" android:layout_width="wrap_content" android:layout_height="wrap_content"/>
</merge>
{% endhighlight %}

Die Integration des Layouts würde somit wie folgt effektiv resultieren:
{% highlight xml %}
<FrameLayout>
  <TextView android:id="@+id/txtView" android:layout_width="wrap_content" android:layout_height="wrap_content"/>
</FrameLayout>
{% endhighlight %}

Das merge-Tag wurde beim inkludieren entsprechend entfernt und das Layout ist somit ohne jegliche unnötige Verschachtelung aufgebaut.