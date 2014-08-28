---
layout: post
title: "Android: Unterschied zwischen px, dp, sp"
---



Neben den eher irrelevanten Maßangen in (Inch), mm (Millimeter) und pt (Point) bilden dagegen die Einheiten px, dp und sp die übliche Basis zur Angabe von Dimensionen.
Hierzu ein kurzer Überblick:

<table>
  <thead>
    <tr>
      <th>Einheit</th>
      <th>Beschreibung</th>
    </tr>
  </thead>
  <tfoot>
    <tr>
      <td>px</td>
      <td>Diese Einheit repräsentiert einen physikalischen Pixel auf dem Display des Benutzers.<br>
      Eine Linie, die mit einer Breite von 2px angegeben wird, beansprucht somit unabhängig vom Geräte-Typ, sowohl auf einem Smartphone 2px in der Breite, als auch auf einem Tablet.</td>

    </tr>
  </tfoot>
  <tbody>
    <tr>
      <td>dp (dip)</td>
      <td>Die Einheit dp (ehemals auch als dip bezeichnet) repräsentiert eine abstrakte Einheit, die je nach Pixeldichte (dpi) variiert und eine Basispixeldichte von 160dpi besitzt.<br>
      Dies bedeutet, dass 1dp auf einem 160dpi Bildschirm letztlich einen Pixel darstellt, dagegen auf einem 320dpi Bildschirm 2px Platz beansprucht.<br>
      Die Einheit skaliert somit gemäß der Pixeldichte und sorgt dafür, dass beispielsweise GUI-Komponenten wie einen Button, optisch für den Benutzer immer gleich groß sind(=Smartphones wie Tablets)
      Eine Linie, die mit einer Breite von 2dp angegeben wird, beansprucht auf einem 160dpi Bildschirm 2px in der Breite, dagegen auf einem 240dpi Bildschirm drei (pysikalische" Pixel), sowie auf einem 320dpi Bildschirm 4 Pixel.</td>

    </tr>
    <tr>
      <td>sp</td>
      <td>Die Einheit sp verhält sich analog der Einheit dp mit nur einem Unterschied: Die Einheit berücksichtigt zudem die vom Benutzer gewünschte und systemweite Schriftgröße.<br>
      Demzufolge wird diese Einheit ausschließlich im Umgang mit Schriftgrößen eingesetzt um somit sowohl die Pixeldichte des Displays zu berücksichtigen, als auch die Benutzereinstellung hinsichtlich gewünschter Schriftgröße zu berücksichtigen.</td>

    </tr>
    
  </tbody>
</table>

Grundsätzlich sollte immer darauf geachtet werden, dass <strong>alle</strong> Dimensionsangaben entweder in der Einheit dp oder sp erfolgen.<br>
Dies garantiert eine möglichst hohe Kompatiblität entlang aller verfügbaren Android-Geräte.
