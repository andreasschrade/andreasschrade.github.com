---
layout: post
title: "Android: Schnelle Einführung in die App-Dekompilierung"
category: "android"
---



## Hintergrund

Der Artikel zeigt, wie sich auf Basis einer .apk-Datei der zugehörige Sourcecode beziehen lässt

### Im Detail

Es gibt unterschiedliche Möglichkeiten, eine Android-Anwendung zu dekompilieren.
Ein Ansatz ist die Verwendung des Tools <a href="https://code.google.com/p/dex2jar/">dex2jar</a>.
Dex2jar ist ein leichtgewichtiges Tool welches entwickelt wurde, um das Dalvik Executable Format (.dex,.odex) zu lesen.

Mit folgenden Schritten lässt sich eine beliebige .apk-Datei dekompilieren

<strong>1. .apk-Datei bezeiehen </strong>

Zuerst gilt es die gewünschte .apk-Datei beziehen. Tools wir App Backup and Restore können in diesem Fall hilfreich sein. Anschließend gilt es die Datei dahingehen umzubennen, dass die einstige .apk-Datei zu einer .zip-Datei wird.

Im Anschluss kann die zip-Datei mit einem nahezu beliebigen Packprogramm entpackt werden.


<strong>2. .dex zu .jar "konvertieren"  </strong>

Sind die Dateien entpackt, müssen lediglich die .dex-Datieen entpackt werden.
Dies geschieht mithilfe des eingangs erwähnten Tools <a href="https://code.google.com/p/dex2jar/">dex2jar</a>, welches eine entsprechende .jar-Datei erstellt.

Beispiel:

C:\test\dex2jar> dex2jar.bat classes.dex

<strong>3. SourceCode betrachten        </strong>

Zum Schluss gilt es nun noch die dekodierte .jar-Datei mit einem beliebigen Java-Decompiler zu öffnen. Dies kann beispielsweise JAD, oder JD-GUI sein

