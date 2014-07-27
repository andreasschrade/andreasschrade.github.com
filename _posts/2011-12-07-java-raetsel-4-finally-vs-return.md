---
layout: post
title: "Java Rätsel #4: finally vs. return"
---




Nachdem schon eine gute Zeit zwischen meinem letzten Rätsel vergangen ist, möchte ich aufgrund einer Idee eines Kollegen, im Mittelpunkt der 2. Adventswoche, ein neues Java Rätsel vorstellen.

Dreh und Angelpunkt dieses Rätsel ist das allzeit bekannte “Try-catch-finally”-Konstrukt, sowie das Keyword return.
Ohne noch viele Worte zu verlieren, folgend das Rätsel:

<strong>Was wird auf der Konsole ausgegeben?</strong>

{% highlight java %}
public class ReturnVsFinally{
    public static void main(String[] args) {
        try {
            return;
        } catch (Exception e) {
            System.out.println("Done catch!");
 
        } finally {
            System.out.println("Done finally!");
        }
    }
}
{% endhighlight %}

<strong>Auflösung:</strong>
Nach der reinen Java-Lehre stehen sich hier zwei Aussagen gegenüber:
* Das “return”-Keyword bricht normalerweise die Ausführung der jeweiligen Methode abrupt ab. Etwaig folgender Code wird dabei innerhalb der Methode ignoriert
* Der finally-Codeblock wird immer, egal ob ein Fehler im Try-Block aufgetreten ist, vor verlassen der Methode aufgerufen

<blockquote>
if there are any try statements within the method or constructor whose try blocks contain the return statement, then any finally clauses of those try statements will be executed, in order, innermost to outermost, before control is transferred to the invoker of the method or constructor. Abrupt completion of a finally clause can disrupt the transfer of control initiated by a
return statement.
</blockquote>

<strong>Nächste Hürde: System.exit()</strong>
Erweitern lässt sich dieses Experiment nun um eine weitere Ausstiegsmöglichkeit aus dem Code: System.exit();

{% highlight java %}
public class FinallyVsExit {
    public static void main(String[] args) {
        try {
            callMe();
            return;
        } catch (Exception e) {
            System.out.println("Done catch!");
        } finally {
            System.out.println("Done finally!");
        }
    }
 
    private static void callMe() {
        System.exit(0);
    }
}
{% endhighlight %}

Erweitert wurde diese Beispiel nun um den Aufruf der statischen Methode “System.exit”, die normalerweise den sofortigen Abbruch der Applikation einleitet.
Was wird passieren? Tatsächlich siegt der Aufruf von System.exit! Das Programm beendet sich, noch bevor eine Ausgabe erfolgen kann.
