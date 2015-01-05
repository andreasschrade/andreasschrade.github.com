---
layout: post
title: "Java: log4j auf einem Blick"
---



## Allgemein
log4j ist ein Logging-Framework in Java. Es gilt aktuell als der De-facto Standard hinsichtlich Logging-Frameworks und findet abseits zu Java auch in einigen anderen Programmiersprachen seine Ableger.

Grundsätzlich besteht die Absicht hinter log4j eine Konfigurationsmöglichkeit zu schaffen, nach der die Log-Verarbeitung zugeschnitten auf die individuellen Bedürfnisse realisiert werden kann.

Entgegen mancher Meinung ist dabei die Log-Verarbeitung sehr effizient; eine typische Log-Ausgabe beansprucht auf modernen Systemen nur wenige Nanosekunden (< 5 ns)

## Log-Level

Folgend eine Auflistung aller unterstützenden Log-Level mit deren Bedeutung:

- ALL: Alle Meldungen werden ungefiltert ausgegeben
- TRACE: "kleinstes" Log-Level; zu detaillierten Debugging-Zwecken gedacht
- DEBUG: zu regulären Debugging-Zwecken gedacht
- INFO: allgemeine Information, die bei Verwendung der Applikation auftreten dürfen und können
- WARN:  Informationen die potenziell schädlich bzw. gefährlich sein können und von der Normalsituation abweichen
- ERROR: verweist auf eine eindeutige Fehlersituation. Das Programm kann dennoch fortgesetzt werden
- FATAL: Kritischer Fehler, eine weitere Programmausführungn ist nicht möglich
- OFF: Deaktiviert das Logging

## Appender

Mittels einem Appender kann festgelegt werden, in welcher Form Log-Informationen verarbeitet werden.

Folgend eine Auflistung der wichtigsten Appender:

- ConsoleAppender: Gibt Logs auf Standardausgabe aus
- FileAppender: Schreibt Logs in eine Datei
- DailyRollingFileAppender: Schreibt Log in eine Datei und wechselt täglich die Datei zu gewissen Zeitpunkten
- RollingFileAppender: Schreibt Log in eine Datei und wechselt die Datei nach einem bestimmten Muster
- SMTPAppender: Verschickt Log-Informationen via Mail
- JDBCAppender: Schreibt Logs in eine Datenbank

## Beispielkonfiguration

Folgend eine einfach Beispielkonfiguration (log4j.properties), die jegliche Log-Files sowohl auf stdout loggt, bzw. ebenso in ein täglich rollierendes Log-File, welches sich im Tomcat-Log-Verzeichnis befindet.

Gerade im Umgang mit Dateien gilt es aufmerksam darauf zu achten, vollständige Dateipfade anzugeben. Unter Umständen gibt es, wenn log4j mit dem Dateipfad nichts anfangen kann, weder ein Log-File, noch einen Fehlerhinweis.

{% highlight xml %}
# Root logger option
log4j.rootLogger=INFO, stdout, DailyRollingFileAppender
log4j.logger.de.example.application=INFO 

# Direct log messages to stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

# Direct log messages to file
log4j.appender.DailyRollingFileAppender = org.apache.log4j.DailyRollingFileAppender
log4j.appender.DailyRollingFileAppender.File = ${catalina.base}/logs/my-app.log
log4j.appender.DailyRollingFileAppender.Append = true
log4j.appender.DailyRollingFileAppender.DatePattern = '.'yyyy-MM-dd
log4j.appender.DailyRollingFileAppender.layout = org.apache.log4j.PatternLayout
log4j.appender.DailyRollingFileAppender.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss} %c{1} [%p] %m%n
{% endhighlight %}
