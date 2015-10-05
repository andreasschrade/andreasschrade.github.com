---
layout: post
title: "Android: Tipps zur besseren App"
category: "android"
---



## Inhalt

Der Artikel richtet sich an Android-Entwickler, die bereits erste Erfahrungen in der Entwicklung für die Android-Plattform gesammelt haben und nun weitergehende "Best-Practices" erfahren möchten, die ihnen helfen, zukünftig etwas smartere Apps zu entwickeln.


### Umgang mit Ressourcen

Ein Best-Practice hinsichtlich Ressourcen ist die Auslagerung in entsprechende Dateien und Verzeichnisse. Unter Ressourcen verstehen sich dabei neben Zeichenketten auch Grafiken, Animationen, Themes, Layouts und Arrays.
Zur besseren Wartbarkeit und Lesbarkeit sollte im darauf geachtet werden, jegliche Ressourcen entsprechend auszulagern.

Folgend ein paar Beispiele:

{% highlight xml %}
<string name="title">App Name</string>
<dimen name="image_width">42dp</dimen>
<color name="text_color">#FF00FF</color>
<array name="timer_values">
   <item>1</item>
   <item>5</item>
   <item>10</item>
</array>
{% endhighlight %}
          
In Umgang mit Zeichenketten existieren zudem zwei weitere, weniger bekannte Funktionen:

<b>Plural-Resourcen:</b><br>
Plural-Resourcen bestehen in den folgenden Ausprägungen: "zero", "one", "many", "other". Mit deren Hilfe erleichtert sich die Handhabung unterschiedlicher Sprachbausteine im Zusammenhang mit der Bildung von Einzahl/Mehrzahl.

{% highlight xml %}
<plurals name="itemCount">
	<item quantity="one">One item</item>
	<item quantity="other">%d items</item>
</plurals>
{% endhighlight %}

Die Abfrage derartiger Ressourcen via Java erfolgt dabei wie folgt:

{% highlight java %}
getResources.getQuantityString(R.plurals.itemCount, itemCount, itemCount);
{% endhighlight %}

Es wird zweimal der Wert "itemCount" übergeben, da dies einmal zur Evaluierung der jeweiligen Ausprägung verwendet wird und einmal der Plathalter in der "other"-Ausprägung gespeist wird.
          
<b>Platzhalter-Formatierung:</b> <br>
String-Ressourcen können zudem reguläre Platzhalter beinhalten, sowie HTML-Tags zur Design-Formattierung. Bei beidem in Kombination müssen letztere maskiert werden:

{% highlight xml %}
<string name="test">&lt;b>Menge: &lt;/b>. %1$s</string>
{% endhighlight %}         
                                                            
### Das Manifest

Im Manifest erfolgt die Registrierung aller Komponenten der App.
Dabei empfiehlt es sich vor allem für Activities:

- die label-Angabe als String-Resource auszulagern
- auf einen individuellen Activity-bezogenen Theme zu verweisen
- zu prüfen, ob die launchMode-Angabe ideal ist
- zu prüfen, ob parentActivityName gepflegt werden sollte

<b>Individueller Theme</b><br>
Die Angabe eines Themes mittels "android:theme" ermöglicht eine Referenz auf eine Style-Resource zu hinterlegen.
Erfolgt keine Activity-bezogene Angabe, wird der gesetzte Application-Theme verwendt. Ist auch dieser nicht gesetzt, wird der System-Theme angewendet.

Es empfiehlt sich, die Activity-Styles durch den Einsatz von Vererbung möglichst klein zu halten, sodass möglichst alle Style-Angaben, die wiederverwendet werden, auf einer Abstraktionsebene liegen.
Näheres zum Aufbau von Styles findet sich im Bereich "Layout & Styles".

<b>Die launchMode-Angabe</b><br>
Das Verständnis über die launchMode-Angabe hilft in manchen Fällen dem Benutzer eine deutlich bessere Activity-Navigation zu bieten.
Insgesamt existieren vier Ausprägungen: "standard", "singleTop", "singleTask", "singleInstance"

Dabei sind allerdings nur die drei erstgenannten Relevant. Die Angabe regelt, inwieweit bestehende Instanzen der Activity wiederzuverwenden sind.
Per Default ist "standard" gesetzt. Dies bedeutet, dass jedesmal wenn ein neues Intent für eine "Standard"-Activity besteht, eine neue Instanz der Activity erzeugt wird.
Dieses Verhalten stellt den Normalfall dar!

Mit "singleTop" verhält es sich dahingehend anders, als dass in dem Fall, wenn ein neues Intent für eine "singleTop"-Activity besteht, nur dann eine neue Instanz der Activity erzeugt wird, wenn die besagte Activity nicht ohnehin am Stack des jeweiligen Tasks oben aufliegt.
Sollte die Activity zu dem ein neues Intent existiert bereits auf dem Task-Stack aufliegen, wird die bestehende Instanz wiederverwendet und die Methode "onNewIntent" aufgerufen.
Am einfachsten erklärt sich diese Option an einem Beispiel:
Angenommen es existiert eine Activity die eine Authentifizierung über eine Webseite benötigt (z.B via oAuth). Damit der Benutzer die Activity verwenden kann, muss er sich zuerst einloggen.
In diesem Fall ruft die Activity via Intent die Login-Seite im Browsers des Benutzers auf. Es erfolgte somit ein Wechsel von der Activity hin zum Default-Browser.
Nach erfolgtem Login lässt sich nun der Rücksprung via einer individuellen callback-Url realisieren (z.B. oauth://myProject). Da die besagte Activity eine Unterstützung für diesen Datentyp im Manifest deklariert hat, wird dank der Angabe von "singleTop" die bestehende Instanz wiederverwendet.
Würde die Angabe von "singleTop" nicht erfolgen und stattdessen die "standard"-Variante angwendet werden, wäre eine neue Instanz der Activity erstellt worden.

Mit "singleTask" verhält es sich analog "singleTop", aber mit dem Unterschied, dass unabhängig der Position im Task-Stack, immer nur eine Instanz existiert.
Prominentestes Beispiel einer derartigen Activity ist die Browser-App. Unabhängig davon, aus welcher App Webseiten aufgerufen werden, so soll immer nur eine Activity/Instanz existieren.

Neben der Deklaration im Manifest lässt sich dieses Verhalten übrigens auch mittels der Intent-Flags "FLAG_ACTIVITY_NEW_TASK", "FLAG_ACTIVITY_CLEAR_TOP" und "FLAG_ACTIVITY_SINGLE_TOP" steuern.

<b>Die parentActivityName-Angabe</b> <br>
Ähnlich wie "launchMode" untersützt auch diese Angabe eine bessere Navigation. Konkret regelt es, welche Activity bei Drücken des Up-Buttons in der Actionbar gestartet werden soll.
Beispielsweise verwendet die GMAIL-App diesen "Up-Button", um aus einer geöffneten E-Mail-Konversation zurück in die Inbox zu gelangen.

Die Angabe der ParentActivity muss dabei einen voll qualifizierten Klassennamen enthalten.

<b>Hardwarebeschleunigung auf älteren Geräten</b><br>
Wenn nicht mindestens minSdk und targetSdk auf 14 oder höher stehen, empfiehlt es sich, die Hardwarebeschleunigung zum rendern der GUI heranzuziehen:
{% highlight xml %}
android:hardwareAccelerated="true"
{% endhighlight %}   

### Design & Styles
Kaum eine Plattform ist von einer derartigen Gerätevielfalt geprägt wie die Android-Plattform.
Aufgrund der unterschiedlichen Geräte, mit unterschiedlichen Displaygrößen und Pixeldichten ist es eine Herausforderung, die Kompatiblität der Benutzeroberfläche einer App für alle Geräte zu ermöglichen.

<b>Grundsatz</b><br>
Grundsätzlich gilt bei der Angabe von Dimensionen immer auf die Einheit dp bzw. bei Schriftgrößen auf sp zurückzugreifen.
Eine kurze Erklärung hierzu:
<table>
  <thead>
    <tr>
      <th>Einheit</th>
      <th>Beschreibung</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>dp (dip)</td>
      <td>Die Einheit dp (ehemals auch als dip bezeichnet) repräsentiert eine abstrakte Einheit, die je nach Pixeldichte (dpi) variiert und eine Basispixeldichte von 160dpi besitzt.<br>
      Dies bedeutet, dass 1dp auf einem 160dpi Bildschirm letztlich einen Pixel darstellt, dagegen auf einem 320dpi Bildschirm 2px Platz beansprucht.<br>
      Die Einheit skaliert somit gemäß der Pixeldichte und sorgt dafür, dass GUI-Komponenten optisch für den Benutzer immer gleich groß sind.</tr>
    <tr>
      <td>sp</td>
      <td>Die Einheit sp verhält sich analog der Einheit dp mit nur einem Unterschied: Die Einheit berücksichtigt zudem die vom Benutzer gewünschte und systemweite Schriftgröße.<br>
      Demzufolge wird diese Einheit ausschließlich im Umgang mit Schriftgrößen eingesetzt.
    </tr>
    
  </tbody>
</table>
  
Weiterhin ist es meist ratsam, ein separates Layout für Smartphone-, als auch für Tablet-Besitzer anzubieten. Die Wiederverwendbarkeit von Layout-Resourcen spielt in diesem Zusammenhang eine große Rolle, die gerade mit Verwendung von Fragments, einem durchdachten Style-Konzept, sowie mit Aufteilung von Layout-XMLs erzielt werden kann.
Fragmets werden ab API-Version 11 (3.0) unterstützt und besitzen einen eigenen Lifecycle. Die Verwendung eines Fragments ist in mehreren Activities möglich. Eine Einführung zu Fragments zu geben würde den Rahmen dieses Artikels deutlich sprengen.
Für interessierte empfehle ich den <a href="http://developer.android.com/guide/components/fragments.html">offiziellen Artikel auf developer.android.com</a>.

<b>Wiederverwendung von Styles</b><br>
Grundsätzlich repräsentiert ein "Style" eine beliebige Anzahl design- und erscheinungsrelevanter Attribute, die unter einer Bezeichnung zusammengefasst und verwendet werden können.

Die Style-Definition werden dabei in der styles.xml (/res/values) gesammelt aufgeführt:

In folgender Style-Angabe wird beispielsweise das Erscheinungsbild der Actionbar definiert:

{% highlight xml %}
<style name="ActionBarThemeOverlay" parent="">
  <item name="android:textColorPrimary">#fff</item>
  <item name="colorControlHighlight">#3fff</item>
</style>
{% endhighlight %} 

Wesentlich im Umgang mit Style-Deklarationen ist, dass möglichst redundante Angaben vermieden werden. Redundante Angaben erschweren nicht nur die Lesbarkeit, sondern erschweren auch die Wartbarkeit, wenn beispielsweise der Farbcode für eine Überschrift nicht nur einmal anzupassen ist, sondern mehrere Male.
Erreichen lässt sich die Wiederverwendbarkeit im wesentlichen auf zwei Arten:

1. Vererbung von Style-Deklarationen
2. Exzessive Nutzung von Style-Verweisen

<b>Vererbung von Style-Deklarationen</b> <br>
Mittels der "parent"-Angabe in einer Style-Deklaration besteht die Möglichkeit, sämtliche Attribute einer bestehenden Style-Deklaration zu übernehmen. Für gewöhnlich verwendet man einen Basis-Style für eine Activity:
{% highlight xml %}
<style name="Theme.MyApp.Base" parent="@style/Theme.AppCompat.Light">
  <item name="android:textColorLink">#f0f0f0</item>
  <item name="android:windowNoTitle">true</item>
  <item name="android:windowBackground">#fff</item>
</style>
{% endhighlight %} 

Jeder weitere, individuelle Style einer Activity verweist nun auf diesen Basis-Theme:
{% highlight xml %}
<style name="Theme.MyApp.SpecialActivity" parent="Theme.MyApp.Base">
    <item name="android:windowBackground">#eee</item>
</style>
{% endhighlight %} 

<b>Nutzung von Style-Verweisen</b> <br>
Gerade im Umgang mit dem Style ein App existieren viele magische Konstanten. Beispielsweise die Farbcodes, die zum Corporate Design gehören.
Diese Angaben gehören sich zweifelsfrei nicht in derartige Style-Deklarationen, sondern sollten stattdessen immer lediglich verwiesen werden:

{% highlight xml %}
<style name="Theme.MyApp.Base" parent="@style/Theme.AppCompat.Light">
  <item name="android:textColorLink">@color/theme_text_color</item>
  <item name="android:homeAsUpIndicator">@drawable/ic_up</item>
  <item name="android:textAppearanceLargePopupMenu">@style/TextAppearance.LargePopupMenu</item>
</style>

<style name="TextAppearance.LargePopupMenu" parent="TextAppearance.AppCompat.Widget.PopupMenu.Large">
    <item name="android:textColor">@color/theme_popup_text</item>
</style>
{% endhighlight %} 

Weiterhin ist es möglich, eigene Style-Attribute zu pflegen, die sowohl innerhalb der eigenen Style-Deklaration, als auch in "Subklassen" zur Verfügung stehen:
{% highlight xml %}
<style name="Theme.MyApp.Base" parent="@style/Theme.AppCompat.Light">
  <item name="colorPrimary">@color/theme_primary</item>
</style>

<style name="Theme.MyApp.Example" parent="Theme.MyApp.Base">
    <item name="android:background">?colorPrimary</item>
</style>
{% endhighlight %} 

Die Referenzierung geschieht auf diese Weise deutlich smarter, erleichtert die Lesbarkeit und im Falle einer Änderung, gilt es lediglich einen Wert anzupassen.

<b>Wiederverwendung von Layouts</b> <br>
Mit Verwendung der Tags &lt;merge&gt; und &lt;include&gt; lässt sich die Wiederverwendbarkeit von Layout-Ressourcen deutlich erhöhen.
Kurz angeschnitten ermöglichen diese, Layout-"Fragmente" zu erstellen, die als Bestandteil in Layout-Ressourcen eingebunden werden können.
Somit besteht beispielsweise die Möglichkeit, ein RelativeLayout als ein solches Layout-"Fragment" mit Verwendung von &lt;merge&gt; einmalig zu definieren und bereitzustellen, um anschließend via &lt;include&gt; in beliebig vielen Layout-Resourcen wiederverwendet werden zu können.
Details zur Verwendung findet sich in einem etwas älteren <a href="http://www.andreas-schrade.de/2014/05/28/android-wiederverwendbare-layouts-mithilfe-von-merge-und-include/">Artikel</a> von mir.

### Umgang mit SharedPreferences
App-spezifische Einstellungen werden in der Regel via SharedPreferences dauerhaft gespeichert.
In diesem Zusammenhang ist es ratsam, den technologischen Aspekt, also die Ermittlung der Preferences-Instanz mitsamt Editor, das Setzen des Wertes sowie den Commit, möglichst von der eigentlichen App-Logik zu trennen.

Eine Möglichkeit dies zu lösen ist eine Art statische Utility-Klasse (ggf. auch via DI integrierbar) zu entwickeln, die jegliche Interaktion mit den SharedPreferences kapselt:

{% highlight java %}
public class PrefUtils  {
  public static final String PREF_USER_IS_MONKEY = "pref_user_is_monkey";
    
  public static void markUserAsMonkey(final Context context) {
      SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(context);
      sp.edit().putBoolean(PREF_USER_IS_MONKEY, true).commit();
  }
}
{% endhighlight %} 

Nun lässt sich aus einer beliebigen Activity recht einfach eine Änderung an der Einstellung vornehmen:
{% highlight java %}
PrefUtils.markUserAsMonkey(ExampleActivity.this);
{% endhighlight %} 

In diesem Zusammenhang ist es besonders ratsam, aussagekräftige Methodenbezeichnungen zu verwenden und nicht das übliche Setter/Getter-Schema anzuwenden.

### Wiederverwendung
Auch in der Android-Entwicklung gilt der Grundsatz, das Rad nicht neu zu erfinden. Frameworks wie Volley oder GSON wurden nicht zum Spaß geschaffen und sollten auch immer - wenn möglich - genutzt werden.

### Tipps zur Verwendung von Activities
Im Umgang mit Activities empfiehlt es sich meist, eine Basis-Klasse zu schaffen, die alle Funktionalitäten enthält, die von mehreren Activities verwendet werden. Dies kann beispielsweise die Verwendung von Google-Analytics sein, Login-Mechanismen (Benutzer darf manche Activities nur in einem bestimmten Zustand aufrufen) oder etwaige Actionbar-Logiken.

Weitere Tipps, im Umgang mit Activities, Services und Widgets folgen demnächst im zweiten Artikel.