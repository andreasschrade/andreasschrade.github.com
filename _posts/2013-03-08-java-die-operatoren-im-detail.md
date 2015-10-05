---
layout: post
title: "Java: Die Java-Operatoren im Detail (Operator-Precedence, Arten, ...)"
category: "java"
---



## Hintergrund
Grundsätzlich kommen Operatoren ins Spiel, wenn eine Wirkung (z.B. Addition) auf ein oder mehrere Operanden angewendet werden sollen. Ein Operand repräsentiert dabei einen Wert auf den der Effekt des Operators angewendet wird und kann beispielsweise eine Variable darstellen.
Zur Realisierung einer solchen Wirkung gibt es nicht nur einen „richtigen“ Operator. Vielmehr lässt sich ein gewünschter Effekt fast immer über unterschiedliche Operatoren realisieren.
Beispielsweise eine Addition um 1:

{% highlight java %}
int a = 1;
a++; // Inkrementierung um 1
a = a + 1; // Addition um 1
{% endhighlight %}

Treffen in einem Ausdruck (Expression) mehrere Operatoren aufeinander, so muss die Reihenfolge der Abarbeitung (Evaluierung) geregelt werden. 
Aus diesem Grund sind alle Operatoren in einer mehrstufigen Prioritätsreihenfolge (operator precedence) eingeordnet.
Dabei gilt:

 - Operatoren mit hoher Priorität werden vor Operatoren mit niedriger Priorität ausgewertet
 - Operatoren mit gleicher Priorität werden grundsätzlich von links nach rechts evaluiert

Ähnlich wie in der Mathematik, besteht die Möglichkeit zur Einflussnahme der Abarbeitungsreihenfolge durch entsprechende Klammersetzung:

{% highlight java %}
int a = 2 * 2 + 1;   // 5
int b = 2 * (2 + 1); // 6 
{% endhighlight %}

Wie sich dem Beispiel entnehmen lässt, gilt auch in Java die aus der Mathematik bekannte Vorrangsregel „Punkt vor Strich“.
Da es im Gegensatz zur Mathematik nicht nur arithmetische Operatoren in Java gibt, besteht zusätzlicher Regelungsbedarf.

### Operator Precedence (Bearbeitungsreihenfolge)

Hierbei gilt:

 - Arithmetische Operatoren (+, -) haben Vorrang vor Vergleichsoperatoren (>=, ==). 
 - Vergleichsoperatoren stehen vor logischen Verknüpfungsoperatoren (&&, &) in der Bearbeitungsreihenfolge.
 - Zuweisungsoperatoren besitzen die geringste Priorität und stehen somit an letzter Stelle der Bearbeitungsreihenfolge. 

Zur Erleichterung finden Sie folgend eine Aufstellung der Operatoren mit deren zugehörigen Priorität:

Priorität 1:<br>
 ++, --, + (Vorzeichen), - (Vorzeichen), ~ (bitweise Nicht), ! (logisches Nicht), () Type-Cast, new (Keyword)

Priorität 2:<br>
 *, /, %

Priorität 3:<br>
 + (Addition), - (Subtraktion), + (String-Verkettung)

Priorität 4:<br>
 <<, >>, >>>

Priorität 5:<br>
 <, <=, >, >=, instanceof

Priorität 6:<br>
 ==, !=

Priorität 7 bis 11:<br>
&, ^, |, &&, ||

Priorität 12:<br>
 ? : (Conditional-Operator)

Priorität 13 (Zuweisungen):<br>
=, *=, /=, +=, -=, %=, <<=, >>=, >>>=, &=, ^=, |=

### Operator-Arten

Grundsätzlich können die aufgeführten Operatoren anhand unterschiedlicher Merkmale kategorisiert werden. Die einfachste Unterscheidung ist die Zuordnung nach der Anzahl der beteiligten Operanden. Bei Java gibt es hierbei drei zu unterscheidende Typen:

<strong>1. unäre Operatoren:  </strong>

Unäre Operatoren beziehen sich auf nur einen Operanden.
In Java fallen darunter 8 Operatoren: 
++, –,+,-,~,!,(),?

<strong>2. binäre Operatoren:         </strong>

Binären Operatoren beziehen sich dagegen auf zwei beteiligte Operanden.
z. B. +, -, /, *

<strong>3. ternäre Operatoren:                 </strong>

Der Bedingungsoperator ist in Java der einzige Operator, welches sich dabei auf drei Operanden bezieht.
