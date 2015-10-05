---
layout: post
title: "Android: Ressourcen-Externalisierung"
category: "android"
---




Ein Best Practice hinsichtlich der Datenhaltung von Ressourcen ist die Auslagerung in entsprechende Dateien/Verzeichnisse.
Das Spektrum an externalisierbaren Elementen reicht dabei von einfachen String-Variablen, bis hin zu Grafiken, Animationen, Themes, Layouts und Arrays.
Gerade zur Verbesserung der Codelesbarkeit, sowie zur einfacheren Wartbarkeit sollte darauf geachtet werden, möglichst alle Ressourcen in die der dafür vorgesehene Struktur auszulagern.

Einfache Daten

Zeichenketten, Zahlen, Farbwerte sowie Dimensionswerte werden bevorzugt in XML-Dateistrukturen abgelegt (res/values-).
<strong>Beispiel:</strong>

{% highlight java %}
<string name="title">App Name</string>
 <dimen name="image_width">23dp</dimen>
 <color name="text_color">#FF00FF</color>
 <array name="timer_values">
   <item>1</item>
   <item>3</item>
   <item>5</item>
   <item>10</item>
 </array>
{% endhighlight %}

Einfache Textformatierungen können über entsprechende aus HTML bekannte Tags formatiert werden:

{% highlight java %}
<string name="formatted_string"><b>WARNING:</b><u>This</u> is <i>very</i> important!</string>
{% endhighlight %}

Bei der Angabe von Farbwerten kann optional vor dem eigentlichen RGB Wert ein Alpha-Wert angegeben werden.
Der Aufbau ist dabei: #AARRGGBB

Dimensionsangaben erfolgen in Android üblicherweise in der Einheit dp (dip – Density Indepent Pixel). Mögliche Werten sind zudem px, in, pt, mm sowie die für Schriftgrößen gewöhnliche konfigurationsabhängige Einheit “sp”.

<strong>Grafiken</strong>

Prinzipiell werden Grafiken innerhalb des drawable-Verzeichnis abgelegt. Meist jedoch werden, um die Bildschirmunabhängikeit zu gewährleisten, unterschiedliche spezielle Verzeichnisstrukturen verwendet.
Das vorangestellte Präfix fungiert dabei als Filter bzw. setzt die Grundlage, ab wann ein Android-Gerät Grafiken aus dem jeweiligen Verzeichnis bezieht.
Generell unterstützt dabei Android Grafiken in den Formaten GIF, JPG sowie PNG Dateien und NinePatch-”Grafiken”.

<table>
<tr>
<td>
res/drawable-ldpi
</td><td>Grafiken für Geräte mit einer geringen Pixeldichte (~120dpi)</td>
</tr>
<tr>
<td>
res/drawable-mdpi</td>
<td>Grafiken für Geräte mit einer mittleren Pixeldichte (~160dpi)</td>
</tr>
<tr>
<td>
res/drawable-hdpi</td>
<td>Grafiken für Geräte mit einer hohen Pixeldichte (~240dpi)</td>
</tr>
<tr>
<td>
res/drawable-xdpi</td>
<td>Grafiken für Geräte mit einer sehr hohen Pixeldichte (~320dpi)</td>
</tr>
<tr>
<td>
res/drawable-nodpi</td>
<td>Grafiken für sämtliche Geräteklassen</td>
</tr>
</table>

Grafiken bei der keinerlei Skalierung erfolgt bzw. überall, auf jedem Gerät mit der gleichen Dimension verwendet werden soll, werden in dem “drawable-nodpi”-Verzeichnis abgelegt.
Ansonsten empfiehlt es sich je nach DPI-Gruppe eine angepasste Grafik in die jeweiligen ldpi-, mdpi-, hdpi- und xdpi-Verzeichnissen abzulegen.
Ich persönlich habe bereits mehrfach (gerade zur Verringerung des Speicherbedarfs) lediglich eine Grafik (nodpi-Verzeichnis) eingesetzt und die Skalierung über den Programmcode selbst erledigt.
Die wesentlichen Informaionen zur Auflösung und Pixeldichte lassen sich recht schnell beschaffen und die Skalierung der Grafiken kann entweder direkt über die Klasse “BitmapFactory” erfolgen, oder aber bei Einsatz der Canvas API über die entsprechende Methodenvariante von “drawBitmap” erfolgen.

<strong>Layouts</strong>

Ähnlich wie bei Grafiken kann auch für das Layout entsprechend der Bildschirmbeschaffenheit unterschiedliche Layout-Dateien verwendet werden.
Neben der bereits bekannten name-qualifiers für die Pixeldichte (ldpi, mdpi, hdpi, nodpi) gibt es gerade mit Android 3.0 eine große Auswahl individueller Filter die es erlauben für jeden Gerätetyp sofern notwendig eine spezielle Layout Variante anzubieten.
Eine kleiner Ausschnitt möglicher Qualifiers:

<table>
<tr>
<td>
res/layout-small
</td><td>Layout für Smartphones mit einer Displaydiagonale kleiner 3.2 Zoll</td>
</tr>
<tr>
<td>
res/layout-medium</td>
<td>Layout für Smartphones mit einer gewöhnlichen Displaygröße</td>
</tr>
<tr>
<td>
res/layout-large</td>
<td>Layout für Tablets</td>
</tr>
<tr>
<td>
res/layout-small-long</td>
<td>Layout für Smartphones mit einer Displaydiagonale kleiner 3.2 Zoll und einem hohen Seitenverhältnis</td>
</tr>
<tr>
<td>
res/drawable-nodpi</td>
<td>Grafiken für sämtliche Geräteklassen</td>
</tr>
</table>

Android kümmert sich hierbei selbstständig um die Wahl der passenden Layout-Komponenten.

<strong>Fazit</strong>

Die im Artikel aufgeführten Möglichkeiten stellen lediglich einen kleinen (wesentlichen) Ausschnitt an externalisierbaren Ressourcen dar.
Neben einfachen 2D-Elementen wie Shapes und Lines besteht ein mächtiges Potenzial für individuell abgestimmte Benutzeroberflächen gerade bei Animationen und Nine-Patches in Verbindung mit sorgfältig gewählt und aufgeteilten Layoutstrukturen