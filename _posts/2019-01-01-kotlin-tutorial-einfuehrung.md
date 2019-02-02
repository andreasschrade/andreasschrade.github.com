---
layout: post
title: "Kotlin Tutorial - Eine Einführung in Kotlin"
comments: true

---


In diesem Artikel möchte ich die noch verhältnismäßig neue Programmiersprache Kotlin, auf eine möglichst leicht verständliche Art und Weise näher bringen.


## 1. Was ist Kotlin

Kotlin ist eine Programmiersprache die hauptsächlich (aber nicht ausschließlich) auf der Java Virtual Machine (JVM) ausgeführt wird und besitzt folgende wesentliche Eigenschaften:

- ausdruckstark (weniger Boilerplate-Code im Vergleich zu Java)
- statisch typisiert (Datentyp ist zur Kompilierzeit bekannt)
- objekt-orientiert
- pragmatisch ausgelegt (Ideal zur "Lösung" alltäglicher Problemstellungen)
- volle Interoperabilität mit Java Code (Java und Kotlin können kombiniert genutzt werden)
- überall einsetzbar, wo momentan auch Java eingesetzt wird 
- open source und unter Apache 2 Lizenz; gehostet auf <a href="https://github.com/jetbrains/kotlin" target="_blank">Github</a>

Die Webseite <a href="https://try.kotl.in" target="_blank">try.kotl.in</a> ist eine Art rudimentäre Online-IDE und ermöglicht somit ein schnelles ausprobieren der neuen Programmiersprache.

### 1.1 Kotlin-Build-Prozess

Java-Code wird in Java-Sourcefiles mit der Dateiendung "java" repräsentiert und mittels <i>javac</i> zu <i>.class</i>-Files kompiliert.

Bei Kotlin wird der Sourcecode in Dateien mit der Endung "kt" repräsentiert. Der Command <i>kotlinc</i> kompiliert Kotlin-Code zu <i>.class</i>-Files. (kotlinc ist somit das Kotlin Gegenstück zu javac)

Egal ob Java-Code oder Kotlin-Code: In beiden Fällen entstehen JVM-kompatible class-Files die beispielsweise in Form einer JAR-Datei gebundelt und ausgeliefert werden. Dieses Verhalten ermöglicht ein nahtlose Interoperabilität zwischen Java- und Kotlin-Code.

### 1.2 Kotlin-IDE

Kotlin lässt sich via Plugin sowohl in Eclipse, als auch in IntelliJ bzw. dem Intellij-Ableger Android Studio entwickeln. Die beste Entwicklungsexperience besteht auf einem IntelliJ-Ableger (z.B. IDEA Community Edition, Android Studio)

## 2. Kotlin Grundlagen

### 2.1 Deklaration von Funktionen

<pre><code class="language-kotlin">
fun main (args: Array&lt;String&gt;) {
    println("Hello world!")
}
</code></pre>

Erklärung:

- Das <i>fun</i>-Keyword deklariert eine Funktion.
- "main" ist der Name der Funktion
- "args" ist der Name des Parameters welcher mit einem Doppelpunkt vom Typ separiert wird.
- Obige Funktion kann als top-level Funktion in einem kotlin-File (.kt) deklariert werden und muss somit nicht in einer Klasse "gewrappt" werden.
- Arrays sind - im Gegensatz zu Java - gewöhnliche Referenztypen.
- "println" ist eine Kurzform (Wrapper) von System.out.println
- es ist kein Semikolon erforderlich!

Bei Funktionen die einen Rückgabewert besitzen wird der Datentyp des Rückgabewetes nach der Parameterliste deklariert: 

{% highlight kotlin %}
fun getString(): String {
    return "mein String"
}
{% endhighlight %}

Im Grunde ergibt dies im Vergleich zur Java Vorgehensweise mehr Sinn:
- Zuerst steht der Input (Parameterliste), dann der Output (Returntyp)
- Die Deklarationsreihenfolge der Bestandteile einer Funktion erfolgt absteigend deren Priorität. Das Wichtigste an einer Funktion ist deren Namen, dann deren Input und zum Schluss erst der konkrete Rückgabetyp.

Bei Funktionen, die nur aus einer Expression bestehen, kann auf dem block body <i>{...}</i> verzichtet werden und stattdessen die Syntax für den expression body angewendet werden

Expression Body:<br>
<pre><code class="language-kotlin">
fun getString(): String = "mein String"
</code></pre>

<b>Expression vs. Statements</b><br>
Expression:<br>
Eine Expression repräsentiert immer einen "evaluierbaren" Wert und kann Bestandteil einer anderen Expression oder Statements sein. Das String-Literal <i>"mein String"</i> ist bsps. eine Expression, da es nach deren Evaluierung einen konkreten Rückgabewert "mein String" besitzt.

Statement:<br>
Im Gegensatz zu Expressions können Statements nicht zu einem konkreten Wert evaluiert werden.
Sie werden außerdem innerhalb eines Code-Blocks deklariert.
Beispielsweise handelt es sich bei allen Loop-Varianten (for, do, while) um Statements.

Unter Kotlin werde u.A. auch Kontrollflusselemente als Expression gehandhabt.
Während unter Java die If-Abfrage als Statement abgebildet ist, fungiert diese unter Kotlin als Expression und ermöglicht somit weitaus mehr Flexibilität. Bsps. kann eine If-Expression als Expression-Body in einer Funktion eingesetzt werden.

<pre><code class="language-kotlin">
fun max (a: Int, b: Int): Int = if (a > b) a else b
</code></pre>

Da zur Kompiliziert der Rückgabetyp von Funktionen via Expression Body bekannt ist, lässt sich obige Funktion dank Typinferenz (type inference) weiter vereinfachen, indem der Rückgabetyp einfach weggelassen wird:

<pre><code class="language-kotlin">
fun max (a: Int, b: Int) = if (a > b) a else b
</code></pre>


to be continued...




