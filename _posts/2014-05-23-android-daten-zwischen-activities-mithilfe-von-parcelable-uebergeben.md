---
layout: post
title: "Android: Komplexe Objekte mithilfe von Parcelable transferieren"
category: "android"
permalink: /android/2014/05/23/android-daten-zwischen-activities-mithilfe-von-parcelable-uebergeben/


---



## Hintergrund

Primitive Datentypen lassen sich leicht unter Android zwischen Activities transferieren.
Der konventionelle Weg, um dagegen Objektreferenzen Activities oder Fragments zu übergeben besteht darin, diese in einem Intent/Bundle zu packen. Dazu reicht es bereits die Daten mit einem entsprechenden Key zu versehen und auf Seiten der lesenden Activity unter Verwendung der entsprechenden Methodenüberladung sowie dem vorab vergebenen Key abzufragen.
Gilt es allerdings eigene komplexe Datentypen auszutauschen, gilt es für den betroffenen Datentypen das Parcelable-Interface zu implementieren.
Wie dies funktioniert und worin die Abgrenzung zum alternativen Serializable-Interface liegt, ist im folgenden Abschnitt beschrieben.

### Implementierung

Im Beispiel verwenden wir die Klasse Customer, die "transferierbar"  implementiert werden soll. Dazu gilt es zuerst das Parcelable-Interface zu implementieren:

{% highlight java %} 
import android.os.Parcel;
import android.os.Parcelable;

public class Customer implements Parcelable { 

  String name;
  int id;
  
  public Customer(String name, int id) { 
    this.name = name;
    this.id = id;
  }
  
  /* Getter und Setter ... */
  public String getName() { ... }
  
  @Override
  public int describeContents() {
    0;
  }
  
  @Override
  public void writeToParcel(Parcel dest, int flags) {
    dest.writeString(name);
    dest.writeInt(id);
  }
  
  private void readFromParcel(Parcel in) {
    name = in.readString();
    id = in.readInt();
  } 
  
  public Customer(Parcel in){
    readFromParcel(in);
  }
  
  public static final Parcelable.Creator<Customer> CREATOR = new Parcelable.Creator<Customer>() {
    @Override
    public Customer createFromParcel(Parcel source) {
      return new Customer(source);
    }
    
    @Override
    public Customer[] newArray(int size) {
      return new Customer[size];
    }
  };
}
{% endhighlight %}

Es fällt auf, dass es sich im Gegensatz zum <em>Serializable-Interface</em> nicht um ein Marker-Interface handelt.
Ganz im Gegenteil, es gilt die Methoden <em>describeContents</em> (in diesem Fall irrelevant) sowie <em>writeToParcel</em> zu implementieren.
Letztere Methode beinhaltet die individuelle Logik zum Schreiben der Instanzvariablen in ein Objekt vom Typ <em>Parcel</em>.

Zudem beinhaltet die Klasse ein statisches Feld namens "CREATOR" welches eine Implementierung von <em>Parcelable.Creator</em> darstellt.
Mit Implementierung von <em>createFromParcel</em> wird letztendlich die Instanziierung von <em>Customer</em> vorgenommen.
Dazu ist es erforderlich, dass die Customer-Klasse selbst einen Konstruktor aufweist, welches ein Objekt vom Typ <em>Parcel</em> erwartet.

Ein Customer Objekt lässt sich somit dem Intent wie gewohnt hinzufügen:

{% highlight java %}
intent.putExtra("customer", new Customer("Max Mustermann", 1));
{% endhighlight %}

Auf der Empfängerseite lässt sich die Objektreferenz mithilfe der Methode <em>getParcelable</em> abrufen:

{% highlight java %}
Bundle data = getIntent().getExtras();
Customer customer = (Customer) data.getParcelable("customer");
{% endhighlight %}

Zugegeben, es bedeutet durchaus viel Aufwand, eine Klasse "parcelable"-fähig zu modifizieren.
Worin liegt also der Mehrwert zur deutlich schneller zu implementierenden Serializable-Alternative?

### Serializable vs. Parcelable

Im Grunde ist der wesentliche Unterschied zwischen beiden Implementierungsmöglichkeit die Performance:
Während die Parcelable-Variante eine individuelle Implementierung erfordert und somit sehr effizient agiert, wird bei Verwendung der Serializable-Variante deutlich Gebrauch von Java Reflection gemacht.
Mit Verwendung der Reflection API entsteht unter Anderem mit Identifizierung der Instanzvariable der betroffenen Klassen verhältnismäßig viele Objekte, die schließlich einen Lauf der Garbage Collection mit sich bringt.
Diversen Performance-Tests zu folge, ist etwa die Parcelable-Variante etwa doppelt so schnell.

