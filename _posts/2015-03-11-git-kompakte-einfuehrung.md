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

Arbeitet man bsps. auf einem Feature-Branch und es gilt nun eine Änderung am Master-Branch vorzunehmen, lässt sich ein Wechsel problemlos mit folgendem Aufruf durchführen.

>git checkout master

Man sollte beim Wechsel von Branches jedoch darauf achten, dass die Staging-Area clean ist um etwaige Konflikte zu vermeiden.
Dies lässt sich realisieren, indem man entweder noch ausstehende Änderungen committet, oder aber Änderungen kurzzeitig "stashed".

<strong>Stashing in Git</strong>

Unter Stashing versteht man hierbei das in der Regel kurzzeitige Speichern von Änderungen des Working-Directory auf einem Stack.

>git stash

Mit Aufruf von "git stash" ist anschließend das Working Directory clean. Ein Wechsel zu einem anderen Branch kann somit bedenkenlos vorgenommen werden.

Um einen Einblick zu erhalten, welche Changes sich auf dem Stash befinden, genügt ein Aufruf von

>git stash list

Möchte man die zuletzt hinzugefügten Änderungen wieder auf das aktuelle Working Directory anwenden, so lässt sich dies mit folgendem Aufruf erzielen

>git stash apply

Befinden sich mehrere Änderungen am Stash, lässt sich die ID der Änderung angeben, die verwendet werden soll.  Folgender Befehl wendet die drittletze (zero-based) Änderung an:

>git stash apply stash@{2}

<strong>Merging in Git</strong>

Sobald man unterschiedliche Branches nutzt, gilt es früher oder später diese Branches zusammenzuführen.
In der Regel werden hierbei Feature-Branches (oder andere Branches abgeleitet vom Master-Branch) zurück in den Master-Branch gemerged.

In Git lässt sich ein Merge mit der Anweisung

>git merge my_branch

realisieren. git merge steht dabei in starkem Zusammenhang mit git checkout.
Mit git checkout wird zuerst auf den Branch gewechselt, in dem der Merge resultieren soll.

Folgendes Beispiel demonstriert das Vorgehen, wenn es einen Feature-Branch in den Master-Branch zu mergen gilt

>git checkout master

>git merge feature123

Nicht immer klappt das Mergen problemlos. Wurden Änderungen an gleicher Position vorgenommen, ist es für Git nicht möglich, automatisch einen Merge durchzuführen.

Folgendes Beispiel behandelt einen derartigen Konflikt. Es wird hierbei versucht einen Feature-Branch zu mergen:

>git merge feature42

Anstelle aber, dass Git automatisch einen Merge-Commit vornimmt, wird der Merge-Prozess unterbrochen und Git meldet sich mit einem Fehler zu Wort:
<i>"Automatic merge failed; fix conflicts and then commit the result."</i>

Um einen Einblick zu erhalten, woran es nun im Detail scheitert, hilft ein Blick auf die Ausgabe von "git status";

Unter dem Bereich "unmerged paths" findet sich eine Auflistung aller Dateien die in einem Merge-Konflikt stehen.
Diese Dateien gilt es nun manuell zu mergen. Ist der Merge abgeschlossen können jene gemergte Dateien wie gewohnt mittels "git add" in den Staging-Bereich aufgenommen werden.

Eine gute Möglichkeit um vor einem Merge bereits einen Überblick zu erhalten, wie groß die "Konfliktgefahr" ist, lässt sich mit Aufruf von

>git diff source_branch target_branch

realisieren. Hierbei wird eine Preview von Git erstellt.


<strong>Tagging in Git</strong>

Es ist für gewöhnlich eine gute Praxis, Software-Releases mit einem Tag zu versehen. Dieses Konzept ist gerade in SVN besonders populär.

>git tag 1.0.0 222efff321

Nach der Tag-Bezeichnung (1.0.0) steht hierbei die Commit-ID.
Diese ID erhält man am einfachsten mittels Aufruf von

>git log

Befinden sich viele Commits im Repo und man möchte sich lediglich einen groben Überblick verschaffen, so ermöglicht ein Aufruf von

>git log --pretty=oneline

den Abruf einer kompakten Commit-Historie.












