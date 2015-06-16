---
layout: post
title: "Dependency Injection mit Spring – Möglichkeiten und Wege"
---


Spring bietet eine vielfältige Möglichkeit zur Realisierung von DI. Welche Arten es gibt, und wie sich diese unterscheiden, ist Gegenstand dieses Artikels.

Wer sich einmal näher mit der Dependeny Injection Funktionalität in Spring beschäftigt hat wird feststellen, dass es allerlei unterschiedliche Annotations zur „Injection“ gibt.
Darunter fallen beispielsweise die Annotations: @Resource, @Inject, @Qualifier, @Autowired

Hin und wieder gilt es eine Bean anhand eines Namen zu injecten. In diesem Zusammenhang bietet sich die @Resource-Annotation von Spring an.
Die Annotation besitzt dabei zwei Ausprägungen: Einmal ohne einem Attribut, sowie eine Ausprägung mit einem Attribut.

{% highlight java %}
public interface OutputWriter {
}

@Component
public class FileOutputWriter implements OutputWriter {	
}

@Component
public class ConsoleOutputWriter implements OutputWriter {
}
{% endhighlight %}

Ohne einem Attribut wird die Bezeichnung der Instanzvariable herangezogen:

{% highlight java %}
@Resource
private OutputWriter writer; // NoSuchBeanDefinitionException

@Resource
private OutputWriter outputWriter; // NoSuchBeanDefinitionException

@Resource
private OutputWriter fileOutputWriter; // injection of FileOutputWriter

@Resource
private OutputWriter consoleOutputWriter; // injection of ConsoleOutputWriter
{% endhighlight %}

Es wird ersichtlich, dass bei Verwendung der Annotation ohne nähere Angabe, einzig die Variablenbezeichnung zur Identifikation der passenden Implementierung herangezogen wird. Wird keine passende Instanz ausfindig gemacht, resultiert dies in einer NoSuchBeanDefinitionException.

Wird die Annotation mit einem Namen versehen, wird die Variablenbezeichnung nicht länger herangezogen:

{% highlight java %}
@Resource(name=“ConsoleOutputWriter“)
private OutputWriter writer; // injection of ConsoleOutputWriter
{% endhighlight %}

Mittels der Qualifier-Annotation lässt sich auf die zu verwendende Bezeichnung Einfluss nehmen:
Die Annotation kann dabei sowohl auf Klassenebene, als auch auf Feldebene verwendet werden.

Auf Klassenebene:

{% highlight java %}
@Component
@Qualifier(„consoleWriter“)
public class ConsoleOutputWriter implements OutputWriter {
}
{% endhighlight %}

Auf Feldebene:

{% highlight java %}
@Resource
@Qualifier(„consoleWriter“)
private OutputWriter outputWriter;  // injection of ConsoleOutputWriter

@Autowired
@Qualifier(„consoleWriter“)
private OutputWriter outputWriter;  // injection of ConsoleOutputWriter

@Inject
@Qualifier(„consoleWriter“)
private OutputWriter outputWriter;  // injection of ConsoleOutputWrite
{% endhighlight %}

Die Verwendung der @Qualifier-Annotation ist auf Klassenebene nicht notwendig, wenn ohnehin bereits die @Component-Annotation eingesetzt wird. Analag zur Qualifier-Annotation erlaubt nämlich auch die @Component-Annotation die Angabe einer Bean-Bezeichnung:

{% highlight java %}
@Component(„consoleWriter“)
  public class ConsoleOutputWriter implements OutputWriter {
}
{% endhighlight %}
Allgemein lässt sich festhalten, dass die @Autowired-Annotation und die @Inject-Annotation sich grundsätzlich gleich verhalten. Beide Annotations benutzen die AutowiredAnnotationBeanPostProcessor-Klasse zur Ausführung der Bean-Injection.
