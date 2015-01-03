---
layout: post
title: "Android: Dialoge unter Android"
---



## Hintergrund

Dialoge unter Android lassen sich aus der Activity mit Aufruf von <em>showDialog(int dialogId)</em> öffnen bzw. anzeigen.
Dialoge werden bei Erzeugung der Activity erstellt und an die jeweilige Activity gebunden. Ein Dialog behält dabei solange den Fokus, bis dieser geschlossen wird.

Die Klasse Dialog stellt dabei die Basisklasse dar. Typische Subklassen sind beispielsweise <em>AlertDialog</em>, <em>TimePickerDialog</em> oder <em>ProgressDialog</em>.

{% highlight java %}
private static final int DIALOG_MESSAGE = 1;

public void onClick(View view) {
    showDialog(DIALOG_MESSAGE);
} 
@Override
protected Dialog onCreateDialog(int id) {
  switch (id) {
    case DIALOG_MESSAGE:
    Builder builder = new AlertDialog.Builder(this);
    builder.setMessage("Möchten Sie die Anwendung wirklich beenden");
    builder.setCancelable(true);
    builder.setPositiveButton("Ich glaube schon", new OkOnClickListener());
    builder.setNegativeButton("Nein, lieber nicht", new CancelOnClickListener());
    AlertDialog dialog = builder.create();
    dialog.show();
  }
  return super.onCreateDialog(id);
}

private final class CancelOnClickListener implements
  DialogInterface.OnClickListener {
  public void onClick(DialogInterface dialog, int which) {
  	// TODO wird ausgeführt bei Klick auf „Ich glaube schon“
  }
}

private final class OkOnClickListener implements
  DialogInterface.OnClickListener {
  public void onClick(DialogInterface dialog, int which) {
  	ExampleActivity.this.finish();
  }
} 
{% endhighlight %}
