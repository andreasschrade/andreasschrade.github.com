---
layout: post
title: "Android: Klickbare Bereich bei ImageButton und transparenten Grafiken"
---



## Hintergrund

Fungieren Grafiken als Buttons, geschieht dies in der Regel unter Verwendung der ImageButton Komponente. Dabei wird mittels <em>android:src</em> auf die eigentliche Grafik verwiesen, während <em>android:background</em> auf <em>@null</em> gesetzt wird.

Kommt nun eine Grafik zum Einsatz, die „transparente Pixel“ beinhaltet, so wird dieser Bereich als nicht klickbar von Android gehandhabt.

Um dennoch diesen Bereich klickfähig zu gestalten, empfiehlt es sich der background-Eigenschaft anstelle von <em>@null</em> schlichtweg einen transparenten Hintergrund zu setzen:

{% highlight xml %}
<ImageButton
        android:id="@+id/btn_menu"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/menu"
        android:background="@android:color/transparent" />
{% endhighlight %}
