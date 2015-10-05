---
layout: post
title: "Dependency Injection mit Spring"
category: "java"
---



## Was ist „Dependency Injection“?
Grundsätzlich gilt der Grundsatz, dass Java Komponenten/Klassen so unabhängig wie möglich von anderen Klassen sein sollten. Durch diese lose Koppelung erleichtert sich die Wiederverwendbarkeit einzelner Klassen. Zugleich erleichtert sich die Unit-Testentwicklung, da durch einer geringeren Abhängingkeit zugleich der Mocking-Aufwand verringert wird.

Die Idee zur Verwirklung dieser loosen Koppelung via Dependency Injection besteht nun darin, dass Klassen bzw. Instanzen möglichst selten selbst Instanzen abhängiger Klassen erstellen. Vielmehr besteht die Absicht darin, dass bestehende Instanzen von „außen“ vorgegeben werden schlichtweg mitverwendet werden.

Ein Beispiel:<br>
Angenommen, die Klasse A hat eine Abhängigkeit zur Klasse B. <br>
Anstelle, dass nun Klasse A mittels „new“-Operator eine eigene Instanz der Klasse B erstellt, wird dafür gesorgt, dass Klasse A eine bestehende Instanz von Klasse B mitverwendet. Klasse A muss in diesem Zusammenhang selbst nicht die konkrete Implementierung von B kennen.

Das zugrundeliegende Konzept hinter Dependency Injection lautet „Inversion of Control“: Eine Klasse (Klasse A) sollte nicht sich selbst konfigurieren, sondern stattdessen von „außen“ konfiguriert werden.

Diese Konfiguration kann im Zusammenhang mit DI dabei auf drei Arten erfolgen: <br>
1) Injection via Konstruktor     <br>
2) Injection via Setter       <br>
3) Direkte Injection via Feld-Annotation     <br>

## DI im Zusammenhang mit Spring
Spring erlaubt die sogenannte Injection von Objekten in andere Objekte. Die Konfiguration kann dabei in Spring sowohl via XML, als auch via Annotations erfolgen. In diesem Artikel möchte ich lediglich die Variante via Annotations vorstellen, da diese in den letzten Jahren stark an Popularität gewonnen hat.

Im folgenden Beispiel geht es um eine Logging-Klasse. Die Klasse besitzt die Aufgabe, je nach Konfiguration, entsprechende Log-Ausgaben auf unterschiedliche Kanäle auszugeben

{% highlight java %}
public interface OutputWriter {
	public void write(String message);
}

public class FileOutputWriter implements OutputWriter {
	public void write(String message) {
		// write message to File
	}
}

public class ConsoleOutputWriter implements OutputWriter {
	public void write(String message) {
		System.out.println(message);
	}
}

public class Logger {
	private OutputWriter writer;
	
	public Logger(){
		writer = new FileOutputWriter();	
	}
}
{% endhighlight %}

Mit der aktuellen Implementierung der Logger-Klasse wird eine starre Verdrahtung zur FileOutputWriter-Implementierung hergestellt. Dank Spring DI lässt sich diese harte Koppelung relativ schnell aufheben:

Hierzu gilt es lediglich
- die zu „injectende“ Klasse mittels „@Component“-Annotation auf Klassenebene zu versehen
- jede „Einsatzstelle“ mit der „@Autowired“-Annotation auf Feldebene zu versehen (Alternativ auch möglich via Setter oder Konstruktor)
- „<annotation-driven />“ in der spring-context.xml bzw. der Spring-Konfigurationsdatei zu hinterlegen um Spring-Annotations zu aktivieren
- Die zu verwendende Implementierung von „OutputWriter“ konfigurieren:
<beans:bean id="outputWriter" class="test.ConsoleOutputWriter">
<beans:bean id="Logger" class="test.Logger">
	<beans:property name="outputWriter" ref="outputWriter" />
</beans:bean>

Der fertige Code:

{% highlight java %}
public interface OutputWriter {
	public void write(String message);
}

@Component
public class FileOutputWriter implements OutputWriter {
	public void write(String message) {
		// write message to File
	}
}

@Component
public class ConsoleOutputWriter implements OutputWriter {
	public void write(String message) {
		System.out.println(message);
	}
}

public class Logger {
	@Autwired
	private OutputWriter writer;
	public Logger(){}
}
{% endhighlight %}

Gilt es nun die Implementierung zu wechseln, muss lediglich die Bean-Assoziation in der Spring-Context-Datei geändert werden.