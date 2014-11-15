---
layout: post
title: "Android: 9-Patch Grafiken kurz vorgestellt"
---



## Hintergrund

Grundsätzlich sind sogennante 9-Patch Grafiken schlichtweg PNG-Grafiken mit Alpha-Kanal. Derartige Grafiken ermögliches es, einen flexiblen Bereich zu definieren, der je nach Skalierung automatisch vergrößert wird.
Typisches Anwendungsgebiet sind beispielsweise Buttons. Es ist ein Unterschied, ob ein Button auf einem schmallen Smartphone oder auf einem 10"-Tablet dargestellt werden soll.
Dank 9-Patch Grafiken ist es möglich, den sich vertikal, als auch horizontal wiederholenden Bereich zu definieren, der im Bedarfsfall zu skalieren ist.

### Im Detail

Auf der linken, sowie oberen Seite der Grafik lässt sich in eine 9-Patch Grafik in schwarz eine Markierung vornehmen, die den Bereich definiert, der skaliert werden soll. Der Bereich abseits der Markierung (die Ecken), bleiben dagegen unangetastet und werden nicht skaliert.
<img style="display:block; " src="{{ site.url }}/assets/2014-04-23-nine-patch-demo.png">
Auf der rechten, sowie unteren Seite lässt sich dagegen der Füllbereich definieren, der mit Inhalt versehen werden kann.
Bei einem Button ist dies der nutzbare Platz, der zur Anzeige von Text bzw. der Button-Beschriftung verwendet werden kann.

Die schwarzen Linien dürfen dabei lediglich eine Stärke von 1px besitzen und werden auf dem Endgerät nicht angezeigt.
Eine 9-Patch Grafik mit einer Größe von 50x50 Pixel, bei der alle Angaben korrekt vorgenommen werden, hat somit zur Laufzeit lediglich eine Größe von 48x48 Pixel.

Wichtig an dieser Stelle ist noch der Hinweis, dass 9-Patch Grafiken lediglich hochskalieren, aber niemals sich in ihrer ursprnglichen Größe verringern. Aus diesem Grund ist es ratsam lieber eine kleinere 9-Patch Grafik, als eine zu große zu erstellen.

Die Definition des Füllbereiches bei 9-Patch Grafiken ist optional. Sie erlauben es auf einfache Art und Weise den nutzbaren Bereich zu definieren. Gerade bei Buttons ist dies eine gute Möglichkeit, präzise den Bereich festzulegen, in dem die Beschriftung platziert werden darf.

Zum Schluss ist noch anzumerken, dass abseits von Buttons es vielzählige Anwendungsgebiete von 9-Patch Grafiken gibt.
Beispielsweise lässt sich dies auch als Hintergrundbild einbeziehen.