---
layout: post
title: "Canvas 2D: Effizientes rendern großflächiger Texturen"
category: "android"
---




Mein Strategiespiel für die Android-Plattform benutzt als Landschaftstextur eine sehr große Hintergrundgrafik.

Da somit die Spielfläche nicht in Tiles unterteilt ist, hat die Hintergrundgrafik nicht selten eine Ausmaße der Breite respektive der Höhe von mehr als 1500 Pixeln. Davon wird allerdings je nach Display-Auflösung und Scroll-Position nur ein Bruchteil der Grafik dem Benutzer angezeigt.  Es liegt daher nahe, anstelle die gesamte Grafik zu rendern, lediglich den für den Benutzer sichtbaren Bereich zu berücksichtigen. Dies spart in diesem Fall aufgrund des Einsatzes von Canvas 2D kostbare Rechenzeit des Prozessors.

Meine Vorgehensweise sieht dabei wie folgt aus:

{% highlight java %}
//scrollingPositionX == Position innerhalb der Map X-Achse
//scrollingPositionY == Position innerhalb der Map Y-Achse
//canvas.getWidth()  == Breite des Viewport
//canvas.getHeight() == Höhe des Viewport

Rect rect1 = new Rect(scrollingPositionX, scrollingPositionY, scrollingPositionX + canvas.getWidth(), scrollingPositionY + canvas.getHeight());
Rect rect2 = new Rect(0, 0, canvas.getWidth(), canvas.getHeight());

canvas.drawBitmap(BitmapTexture, rect1, rect2, new Paint());
{% endhighlight %}

Zuerst wird ein Objekt vom Typ Rect erstellt, welches die Dimensionen des Ausschnittes repräsentiert, der von der “übergroßen” Landschaftstextur angezeigt werden soll.
Das zweite Objekt vom Typ Rect repräsentiert hierbei den Zielbereich. In diesem Fall soll dies bildschirmfüllend gerendert werden.

Die Methode “drawBitmap” veranlasst letztlich die Zeichnung der Grafik.

![Performance]({{ site.url }}/assets/2013-08-09-canvas-performance.jpg)