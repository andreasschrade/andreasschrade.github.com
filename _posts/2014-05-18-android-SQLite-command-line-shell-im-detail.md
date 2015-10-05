---
layout: post
title: "Android: Remote-Zugriff auf die SQLite Datenbank"
category: "android"
---



## Hintergrund

SQLite ist ein essentieller Bestandteil der Android-Platform und dient generell zur Speicherung aller App-bezogenen Daten.
Gerade zur Entwicklungszeit kann es durchaus nützlich sein, direkt SQL-Queries abzusetzen bzw. direkt einen Blick auf die Datenbank bzw. die Tabellenstrukturen zu werfen.

### Vorgehensweise

Unter Windows befindet sich die SQLite Kommandozeile im Android SDK (sqlite3.exe) und sollte entsprechend dank gesetzter Umgebungsvariable bereits von der Kommandozeile aus aufrufbar sein.

<strong> Datenbank erzeugen </strong>

Um eine neue Datenbank auf dem verbundenen Gerät zu erzeugen, muss lediglich der Datenbankname angegeben werden:

{% highlight sql %} 
sqlite3 Customer.db
{% endhighlight %}

<strong> Tabelle erzeugen </strong>

Nach erstellter Datenbank lässt sich eine Tabelle mithilfe des üblichen SQL-CREATE-Query erzeugen:

{% highlight sql %} 
sqlite> create table Customer (Id int primary key, Name varchar(42));
{% endhighlight %}

<strong> Records einfügen </strong>

Auch das einfügen von Records erfolgt wie gewohnt mittels INSERT-INTO-Statement:

{% highlight sql %} 
sqlite> insert into Customer values('0','Max');
sqlite> insert into Customer values('1','Bruce');
sqlite> insert into Customer values('2','Dirk');
{% endhighlight %}

<strong> Records abfragen </strong>

Das Abfragen von Records erfolgt mittels SELECT-Statement:

{% highlight sql %} 
sqlite> select * from Customer;
{% endhighlight %}

<strong> Tabellen-Dump </strong>

Ein überaus nützliches Feature ist der .dump-Befehl, mit dessen Hilfe sich der Inhalt der Tabelle ausgeben lässt:

{% highlight sql %} 
sqlite3 Customer.db .dump
{% endhighlight %}
oder auch
{% highlight sql %}
sqlite3 Customer.db
sqlite> .dump
sqlite> .quit
{% endhighlight %}

Der Output ist in beiden Fälle folgender:

{% highlight sql %}
PRAGMA foreign_keys=OFF;
BEGIN TRANSACTION;
CREATE TABLE Customer (Id int primary key, Name varchar(42));
INSERT INTO Customer VALUES(0,'Max');
INSERT INTO Customer VALUES(1,'Bruce');
INSERT INTO Customer VALUES(2,'Dirk');
COMMIT;
{% endhighlight %}

Zudem ist mittels .schema-Befehl es ebenso möglich, die Tabellenstruktur als SQL-CREATE-Statement ausgeben zu lassen.
Gerade letzteres ermöglicht es schließlich, den Query in eine dedizierte Datei schreiben zu lassen:

{% highlight sql %} 
sqlite3 Customer.db .schema > Customer.sql
{% endhighlight %}