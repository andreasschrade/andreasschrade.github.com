---
layout: post
title: "Android: Mails aus App versenden"
---



## Hintergrund

Eine einfache Möglichkeit um dem Benutzer eine Kontaktmöglichkeit anzubieten ist der bewährte Weg über die eMail.
In diesem Beitrag wird folgend eine denkbar simple Möglichkeit vorgestellt, wie der Versand über einen Mail-Clienten am Smartphone des Benutzers erfolgt.

### Implementierung

Der einfachste Weg eine Mail zu versenden führt uns letztlich zur Nutzung eines am Smartphone des Benutzers vorinstallierten Mail-Clients.
Um mittels dem installierten Client eine Mail zu versenden, genügt es bereits, das Intent <em>ACTION_SEND</em> zu verwenden sowie als Intent-Type <em>message/rfc822</em> anzugeben.

Weiterhin wird mit

 - EXTRA\_EMAIL der/die Empfänger der Mail als String-Array angeben
 - EXTRA\_SUBJECT der Betreff der Mail bestimmt
 - EXTRA\_TEXT der Inhalt der Mail angegeben
 - EXTRA\_STREAM eine URI zu einer Datei angegeben, die als Attachement der Mail beigefügt werden soll

Nach Angabe der erforderlichen Parameter kann bereits der Versand erfolgen:

{% highlight java %}
Intent i = new Intent(Intent.ACTION_SEND);
i.setType("message/rfc822");
i.putExtra(Intent.EXTRA_EMAIL  , new String[]{"empfaenger@example.de"});
i.putExtra(Intent.EXTRA_SUBJECT, "Betreff der Mail");
i.putExtra(Intent.EXTRA_TEXT   , "Eigentliche Nachricht");
try {
    startActivity(Intent.createChooser(i, "Sende Mail..."));
} catch (android.content.ActivityNotFoundException ex) {
    Toast.makeText(ExampleActivity.this, "Es sind keine Mail-Clients installiert, weshalb die Mail nicht versendet werden kann.", Toast.LENGTH_LONG).show();
}
{% endhighlight %}

Der Benutzer wird nun aufgefordert einen entsprechenden Mail-Clienten zu wählen.
Im Falle, dass kein Client installiert ist, wird die ActivityNotFoundException gefangen und dem Benutzer via Toast ein Hinweis diesbezüglich angezeigt.

<strong>Anhang beifügen</strong>

Falls ein Attachement beigefügt werden soll, muss das bereits angesprochene Intent-Extra <em>EXTRA_STREAM</em> angegeben werden:

{% highlight java %}
i.putExtra(Intent.EXTRA_STREAM   , Uri.parse("file://Pfad-zur-Datei"));
{% endhighlight %}

<strong>HTML-Mail versenden</strong>

Prinzipiell verhält es sich mit Versand einer HTML-Mail ähnlich wie mit dem Versand einer einfachen Text-Mail.
Allerdings wird als Intent-Type anstelle von <em>message/rfc822</em> der Typ <em>text/html</em> angegeben.
Mittels <em>Html.fromHtml(body)</em> wird schließlich der HTML-Content in den Mail-Body eingefügt:

{% highlight java %}
Intent i = new Intent(Intent.ACTION_SEND);
i.setType("text/html");
i.putExtra(Intent.EXTRA_EMAIL  , new String[]{"empfaenger@example.de"});
i.putExtra(Intent.EXTRA_SUBJECT, "Betreff der Mail");
i.putExtra(Intent.EXTRA_TEXT   ,  Html.fromHtml(body));
try {
    startActivity(Intent.createChooser(i, "Sende Mail..."));
} catch (android.content.ActivityNotFoundException ex) {
    Toast.makeText(ExampleActivity.this, "Es sind keine Mail-Clients installiert, weshalb die Mail nicht versendet werden kann.", Toast.LENGTH_LONG).show();
}
{% endhighlight %}