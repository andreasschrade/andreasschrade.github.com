---
layout: post
title: "Java: Vergleich von Fließkommazahlen"
---



## Hintergrund
Wird double oder float als Datentyp verwendet, so sind Wertvergleiche mittels dem Vergleichsoperator auf identische Übereinstimmung zu vermeiden.
Weshalb dies so ist und welche Möglichkeiten es dennoch zum sicheren Wertevergleich gibt, dass erfahren Sie im folgenden Abschnitt:

### Vergleich vom Fließkommazahlen
Der Vergleich von double oder float birgt ein hohes Fehlerpotential, da schließlich eine Gleitkommazahl nur selten identisch dargestellt und somit korrekt verglichen werden kann:

{% highlight java %}
double a = 1.1 + 1.1;
double b = 3.3 - 1.1;
System.out.println(a == b); // false
{% endhighlight %}

Aus diesem Grund empfiehlt es sich bei Vergleichen einen Puffer zu definieren, um dem der Wert abweichen darf. Üblicherweise ist dieser Puffer eine positive Zahl und ist weitgehend unter dem Begriff Epsilon bekannt.

{% highlight java %}
public static boolean compare(double a, double b, double epsilon){
  Math.abs(a - b) < epsilon;
}

double a = 1.1;
double b = 1.2 - 0.1;
System.out.println(compare(a, b, 0.0000001));
{% endhighlight %}

Wie hoch der Puffer (Epsilon) sein sollte, hängt sowohl von der jeweiligen Situation, als auch von dem Grad der „gedulteten“ Abweichungen mit der zu rechnen ist ab.
Die Implementierung der Methode <em>compare</em> repräsentiert dennoch gerade einmal die Minimalimplementierung zum vergleichen von Gleitkommazahlen. Ein konsistenter Algorithmus der Zustände wie NaN (Not a Number 10 /0 = NaN) und ähnliche zusätzliche Bedingungen berücksichtigt übersteigt weit den bestehenden einzeiligen Code. Es ist deshalb ratsam, bestehende Implementierung zum Vergleich von Gleitkommazahlen zu verwenden.
Während für diesen Zweck erstaunlicherweise keine Methode im Java-Reportiore zu finden ist, gibt es glücklicherwiese Libraries wie Google Guava (DoubleMath.fuzzyCompare(double a, double b, double tolerance) sowie Apache Commons Math Library (MathUtils.equals(double x, double y, int maxUlps) mit deren Hilfe zuverlässige Ergebnisse ermittelt werden können.
