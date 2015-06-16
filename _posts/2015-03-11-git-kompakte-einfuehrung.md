---
layout: post
title: "Schnelle Einführung in Git"
---

## Was ist Git?

Bei Git handelt es sich um ein dezentrales „Version Control System“, welches ursprünglich für die Sourcecode-Verwaltung des Linux-Kernels von Linus Torvald entwickelt wurde. 

Grundsätzlich verfolgt ein „Version Control System“ die Absicht, gemeinschaftlich an einer Liste an Dateien zu arbeiten. Für gewöhnlich findet ein solches VCS seine Anwendung in der Softwareentwicklung und ermöglicht eine gemeinsame Entwicklung einer Code-Basis.

Näher betrachtet kümmert sich ein VCS um die Aufzeichnung sämtlicher Veränderungen aller Dateien. Es ermöglicht somit transparent festzuhalten, welche Person, zu welchem Zeitpunkt, welche Änderung an einer oder mehrerer Datei vorgenommen hat.

Ein „dezentrales“ VCS zeichnet sich im Gegensatz zu einem zentralen VCS darin aus, dass jeder Entwickler eine vollständige Kopie des gesamten Repository besitzt. Das bedeutet, dass kein zentraler Server notwendig ist. Dieser Aspekt macht ein dezentrales VCS schlichtweg ausfallsicher: Während bei einem zentralen VCS bei Ausfall des Servers die Weiterentwicklung stark gehemmt wäre, ist der Schaden bei einem dezentralen VCS dagegen prinzipiell nicht existent!


<strong>Klonen eines Repository</strong>

Damit ein Entwickler sich an der Entwicklung eines auf Git basierenden Entwicklungsprojekts beteiligen kann, gilt es zuerst Git zu installieren und anschließend die bisherige Code-Basis zu beziehen. In diesem Zusammenhang wird auch mit dem „klonen“ eines Repository gesprochen.

>git clone benutzername@https://meine-domain.de/git/mein-repository

Obiger Befehl weist Git an, eine Kopie des Repository zu beziehen, welches sich unter der Adresse https://meine-domain.de/git/mein-repository befindet. Der Entwickler besitzt somit eine vollständige Kopie des Repository mit der gesamten Änderungshistorie.


<strong>Der Workflow</strong>

Das zuvor bezogene Repository existiert nun – wie angesprochen – ebenso in einem lokalen Repository des Entwicklers. Wie setzt sich allerdings das lokale Repository zusammen?

Im Detail verbergen sich dahinter drei „Instanzen“:

1. Working Directory (Arbeitskopie):  Die Arbeitskopie ist sozusagen jene Stelle, an der sich die Dateien auf dem Dateisystem befinden und somit verändert werden können.

2. Staging Area (Index): Genau genommen handelt es sich für Git bei der Staging Area lediglich um eine Datei, die festhält, welche Dateien für den nächsten Commit herangezogen werden sollen. Es handelt sich daher um die Zwischenstufe zwischen einer lokalen Veränderung und dem eigentlichen Commit der Änderung.

3. HEAD (.git Verzeichnis):  Zu jedem Git-Repository findet sich ein „.git“-Verzeichnis. Darin werden etwaige Metadaten abgelegt und die eigentliche VCS-Datenbank gespeichert. Der HEAD zeigt letztendlich auf den letzten Commit.

Wie spielen diese drei Instanzen nun zusammen?

Beim Klonen eines Repositorys wird streng genommen zuerst die eigentliche Datenbank im .git-Verzeichnis aufgebaut und anschließend die Dateien in die Arbeitskopie entpackt.

Wird nun nach dem Klonen eine Änderung an einer Datei (Test.java) vorgenommen, befindet sich diese Änderung erstmal nur in der Arbeitskopie. Der Status der Datei ist „modified“.

Im nächsten Schritt gilt es diese Datei für den Commit vorzusehen bzw. vorzuschlagen. Dies geschieht mittels:

>git add Test.java

Die Datei befindet sich somit in der Staging Area und besitzt den Status „staged“.

Mit Aufruf von

>git add *

könnten auch alle veränderten Dateien gleichzeitig zum Index hinzugefügt werden.

Erst, wenn der commit-Befehl aufgerufen wird, werden alle Dateien, die sich zu jenem Zeitpunkt auf „staged“ befinden, in das lokale Repository commitet.

>git commit -m „Meine Commit-Nachricht z.B. changed Test file“

Mit Aufruf des commit-Befehls befinden sich nun die Änderung im HEAD des lokalen Repository. Wichtig an dieser Stelle ist der Hinweis, dass die Änderung noch nicht im Remote-Repository vorliegt. Das bedeutet, dass die Änderung – bildlich gesprochen – nach wie vor noch auf dem Rechner des Entwicklers liegt, der die Änderung vorgenommen hat.

Um diesen Zustand zu ändern und die Änderung global zugänglich zu machen, gilt es einen sogenannten „push“ durchzuführen. Damit wird die Änderung an das entfernte (remote) Repository übertragen:

>git push origin master

Die Bezeichnung „master“ definiert hierbei den Namen des Branch. Dieser kann unter Umständen abweichen. Hierzu später mehr.

Als nächsten Schritt gilt es Änderungen anderer Entwickler zu beziehen.

Dies geschieht mit Aufruf von

>git pull

Mit Ausführung obigen Befehls wird Git angewiesen, Änderungen vom Remote-Repository aus dem „master“-Branch zu beziehen. Im Idealfall klappt dieser Schritt problemlos. Liegen jedoch noch nicht committete Veränderungen vor, die mit eingehenden Änderungen kollidieren, kommt es zum sogenannten Konflikt. Grundsätzlich versucht Git selbständig Dateikonflikte zu beheben, in dem es unterschiedliche Dateiversionen miteinander „merged“. Leider klappt dies nicht in allen Fällen. Nach dem manuellen Merge gilt es dies Git mitzuteilen indem

>git add <dateiname>

aufgerufen wird.


<strong>Branches in Git</strong>

Git unterstützt das sogenannte Branching: Darunter versteht sich das parallele halten unterschiedlicher Versionen an Dateien. Das bedeutet, dass schlichtweg mehrere Versionen einer Datei existieren können.

Dieses Feature ist beispielsweise nützlich, wenn eine neue Funktion implementiert werden soll, die für ein Release in bsps. einem Monat vorgesehen ist, zugleich aber die reguläre Entwicklung für das Release in einer Woche nicht beeinträchtigen soll.
Mit Branches wird somit eine isolierte Entwicklung ermöglicht. 

Ein Branch lässt sich relativ einfach erstellen:

>git checkout -b feature_123

Obiger Befehl weist Git an, einen Branch namens „feature_123“ zu erstellen und zugleich zum besagten Branch zu wechseln.

Damit der neu angelegte Branch allen Entwicklern zugänglich ist, muss dieser in das Remote-Repository übertragen werden. Dies geschieht mit Aufruf von:

>git push origin feature_123

Löschen lässt sich der Branch dagegen mit Aufruf von:

>git branch -d feature_123

