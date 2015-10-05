---
layout: post
title: "Android: Möglichkeiten zum Background-Processing im Vergleich"
category: "android"
---



## Hintergrund

Android bietet dem Entwickler verschiedene Möglichkeit um klassisches Background-Processing abzubilden.
In diesem Beitrag möchte ich die einzelnen Möglichkeiten kurz in einer Tabelle veranschaulichen und gegenüberstellen.

### Vergleich

Bevor es zum Vergleich der einzelnen Möglichkeiten geht möchte ich noch anmerken, dass jegliche Verarbeitung innerhalb der Service-Klasse unter Android im Main-Thread ausgeführt wird, sofern kein separater Worker-Thread innerhalb des Service gestartet wird. Das ist ein wichtiger Hinweis, der häufig - gerade von Anfängern - missverstanden wird. Die Verwendung eines "rohen" Service ermöglicht lediglich jegliches Processing, welches nicht an die UI gebunden ist zu bearbeiten. Dies klappt soweit auch gut, sofern das Processing auch nur wenige Sekunden in Anspruch nimmt.
Langfristige Tasks müssen immer in einem Thread-Konstrukt ausgelagert werden, welches nicht im Main-Thread läuft.

Die folgende Tabelle vergleicht die Konstrukte Service, Thread, IntentService sowie AsyncTask:

<table>
<tr>
   <th>&nbsp;</th>
   <th>Service</th>
   <th>Thread</th>
   <th>IntentService</th>
   <th>AsyncTask</th>
</tr>
<tr>
  <td>Verwendungszweck</td>
  <td>wenn Aufgaben nicht an die UI gebunden sind und sehr schnell bearbeitet werden</td>
  <td>bei langlaufenden (ggf. parallelisierten) Operationen die keine "Abhängingkeit" zum Android-Lifecycle besitzen</td>
  <td>bei (langlaufenden) Operationen die sequentiell (FiFo) bearbeitet werden soll</td>
  <td>bei (langlaufenden) Operationen die Kommunikation mit Main-Thread erfordern</td>
</tr>

<tr>
  <td>Aufruf</td>
  <td>via onStartService()</td>
  <td>via start()-Methode der Thread-Instanz</td>
  <td>via Intent</td>
  <td>via execute()-Methode der jeweiligen Instanz</td>
</tr>

<tr>
  <td>Aufrufbar von</td>
  <td>beliebigem Thread</td>
  <td>beliebigem Thread</td>
  <td>Main-Thread</td>
  <td>Main-Thread</td>
</tr>

<tr>
  <td>Vorteile/Nachteile</td>
  <td>- blockiert Main-Thread</td>
  <td>- manuelles Thread-Management erforderlich<br>- erschwert Lesbarkeit<br>+ erhöhte Flexibilität</td>
  <td>+ integriert Queueing</td>
  <td>- eine Instanz kann nur einmal aufgerufen werden</td>
</tr>

</table>

<strong> Fazit: </strong>

Grundsätzlich hat jede Implementierung seine Daseinsberechtigung. Dennoch sticht der IntentService bei jeglicher sequentiellen Hintergrundverarbeitung die keine UI-Interaktion beinhaltet hervor.
Ebenso bietet der Async-Task eine leistungsfähige Möglichkeit um zeitintensive Prozesse die unmittelbar mit der Benutzeroberfläche verwoben sind abzubilden.
Eine eigene Thread-Implementierung eignet sich dann, wenn es wirklich um "einfache" plain-old-java Hintergrundverarbeitung geht, die keinerlei "Android-Abhängigkeiten" (z.B. Lebenszyklus) aufweist.
