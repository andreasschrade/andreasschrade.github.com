---
layout: post
title: "Android: Möglichkeiten zur Systemzeit- und Uptime-Ermittlung"
---



## Hintergrund

Android stellt mit der Klasse <em>SystemClock</em> verschiedene Möglichkeiten zur Verfügung, die Systemzeit sowie Uptime  zu ermitteln.

### System.currentTimeMillis()

Der Aufruf von <em>System.currentTimeMillis()</em> stellt den regulären Fall dar, die aktuelle Systemzeit zu beziehen.

Der Rückgabewert entspricht die Anzahl vergangener Millisekunden, die seit der Unix Epoche (01.01.1970 UTC) vergangen sind. Die lokale Zeitzone wird dabei nicht berücksichtigt. Ebenso werden keine Schaltsekunden berücksichtigt.
Die Zeitangabe variiert entsprechend der eingestellten Zeit des Benutzers. Der Wert kann somit beliebig variieren weshalb vorsicht geboten ist, diesen Wert zur Ermittlung von technischen Zeitspannen zu berücksichtigen die zur Aufrechterhaltung der Programmausführung notwendig sind.

Eine Möglichkeit auf Device-Zeitänderungen zu reagieren ist das "lauschen" auf die Broadcasts <em>ACTION_TIME_TICK</em>,<em>ACTION_TIME_CHANGED</em>,<em>ACTION_TIMEZONGE_CHANGED</em>


### SystemClock.uptimeMillis()

Gibt die verstrichene Zeit seit dem Bootprozess in Millisekunden an. Die Angabe schließt allerdings Standby-Phasen aus bzw. die Zeit wird nicht hochgezählt, wenn sich der Prozessor im Tiefschlafmodus befindet.

### SystemClock.elapsedRealtime()

Gibt die verstrichene Zeit seit dem Bootprozess in Millisekunden an und berücksichtigt ebenso Tiefschlafphasen bzw. Standby-Phasen.
Analog existiert mit <em>SystemClock.elapsedRealtimeNanos()</em> ein weiterer Aufruf der die verstrichene Zeit in Nanosekunden liefert.