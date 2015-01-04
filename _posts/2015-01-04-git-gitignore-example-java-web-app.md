---
layout: post
title: "Git: .gitignore Vorlage für Java Web-Applikationen"
---



## Allgemein
Wer Git nutzt kennt vermutlich das Problem: Gerade zu Beginn eines neuen Projektes werden etliche Dateien zum "commit" "vorgeschlagen" bzw. stören im Umgang mit Git, die keineswegs im Repository landen sollen.

Aus diesem Grund habe ich folgend eine Vorlage erstellt, die gerade in Hinblick für Java-Web-Applikationen äußerst hilfreich sein dürfte.


## Vorlage .gitignore

Folgende Vorlage behandelt allgemeine Java-Klassen und spezifische Dateien von Eclipse, IDEA, Gradle, Maven sowie Windows:

{% highlight xml %}
# Ignore class files
*.class

# Package Files #
*.jar
*.war
*.ear

# Eclipse project data
.classpath
.project

# Maven specific files
target/
pom.xml.tag
pom.xml.releaseBackup
pom.xml.versionsBackup
pom.xml.next
release.properties

# Gradle specific files
.gradle
/build
/app/build

# IDEA files
.idea
source.iml
/.idea/workspace.xml
/.idea/libraries
.DS_Store

# Eclipse (general files)
.metadata
bin/
tmp/
*.bak
local.properties
.settings/
.loadpath
*.tmp

# Windows specifi files
Thumbs.db
ehthumbs.db
Desktop.ini

{% endhighlight %}
