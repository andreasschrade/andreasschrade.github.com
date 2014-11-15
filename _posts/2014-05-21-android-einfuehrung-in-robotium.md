---
layout: post
title: "Android: Automatisiertes Testing mit Robotium"
---



## Hintergrund



Robotium ist ein Test-Framework welches auf einfache Art und Weise die Erstellung automatisierter Black-Box-Tests für Android ermöglicht.
Mit Robotium ist es möglich, Testfälle für Funktionstest, Systemtest, Akzeptanztests unter Einbezugnahme mehrerer Activities zu realisieren.

Alle notwendigen Informationen die zum Start erforderlich sind, sind in folgenden Abschnitten erklärt.

Mehrwert von Robotium in Kürze:
- Testentwicklung erfordert nur weniges technisches Wissen der zu testenden Anwendung
- Das Framework handelt mehrere Activities automatisch
- Mit geringem Zeitaufwand lassen sich bereits solide Testfälle erstellen
- Lesbarkeit von Testfällen ist überdurchschnittlich gut
- Testfälle sind relativ robust, da das GUI-Binding zur Laufzeit geschieht
- Schnelle Testausführung
- Sehr gute Maven und Ant Integration

### Erstes Beispiel


Um stabile und verlässliche Tests zu entwickeln bietet Robotium viele Methoden, die sich auf grafische Elemente einer App beziehen:

clickOnText(“Login”);
clickOnButton(“Save”);
searchText(“Logout”);
goBack();
getButton();
isRadioButtonChecked();
…

Mit diesen einfachen Methoden können bereits in Verbindung mit JUnit sehr schnell robuste Testfälle erstellt werden.

Erstes Beispiel:

<strong>1. Robotium herunterladen </strong>

Um Robotium verwendne zu können, wird die letzte stabile Version des Test-Frameowrks benötigt. Diese findet sich auf der <a href="https://code.google.com/p/robotium/downloads/list" target="_blank">Downloadseite des Projekts</a>. Die JAR-Datei lässt sich anschließend wie gewöhnlich in das Projekt integrieren.   

<strong>2. Neues Projekt erstellen </strong>

Um die Fähigkeiten von Robotium zu testen, wird zuerst ein neues Projekt benötigt. Anschließend gilt es die zuvor bezogene JAR-Datei in das Projekt einzubinden.

<strong>3. Testfallerstellung </strong>

Als Beispiel gilt es folgende Activity mit Robotium zu testen:

{% highlight java %}
package com.example;

import android.os.Bundle;
import android.app.Activity;
import android.view.Menu;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

public class TestActivity extends Activity {

	private Button btn;
	private EditText et;
	private TextView tv;
	
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);

		btn= (Button)findViewById(R.id.btn1);
		et= (EditText)findViewById(R.id.edit1);
		tv= (TextView)findViewById(R.id.txt1);

		btn.setOnClickListener(new View.OnClickListener() {

			@Override
			public void onClick(View v) {
				Toast.makeText(getApplicationContext(), "Button geklickt!", Toast.LENGTH_LONG).show();
				tv.setText(et.getText().toString());
			}
		});
	}

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		getMenuInflater().inflate(R.menu.main, menu);
		return true;
	}

}
{% endhighlight %}

Mit Klick auf den Button wird ein Toast angezeigt und der Wert, der in Eingabefeld edit1 hinterlegt ist, wird in die TextView txt1 übertragen.


Die Hauptklasse, die einem im Umgang mit Robotium begegnet ist Solo. Solo wird mit Erstellung des Testfalls und Test der ersten Activity initialisiert.

In folgendem TestCase wird Solo in setup() initialisiert. Anschließend erfolgt im ersten Testfall (<i>testCase()</i>) mithilfe von Solo die Bezugnahme auf die einzelnen GUI-Komponenten. Beispielsweise wird mit solo.getView() respektive dem anschließend Cast das Eingabefeld edit1 bezogen.
In Kombination mit den üblichen JUnit-Bordmitteln ist es damit möglich, eine beliebige Test-Eingabe sowie den Button-Klick zu simulieren und das Resultat auf Korrektheit zu prüfen.

{% highlight java %}
package com.example;
 
 
import com.robotium.solo.Solo;
import android.test.ActivityInstrumentationTestCase2;
import android.widget.EditText;
import android.widget.TextView;
 
public class MyTestClass extends ActivityInstrumentationTestCase2<TestActivity>{
 
 
    private Solo solo;
 
    public MyTestClass() {
        super(TestActivity.class);
    }
 
    public void setUp() throws Exception {
        solo = new Solo(getInstrumentation(), getActivity());
    }
 
    public void testCase() throws Exception {
 
        String vResult="TestExample";
        EditText vEditText = (EditText) solo.getView(R.id.edit1);
        solo.clearEditText(vEditText);
        solo.enterText(vEditText,"TestExample");
        solo.clickOnButton("Submit");
 
        assertTrue(solo.searchText(vResult));
        TextView textField = (TextView) solo.getView(R.id.txt1);
        assertEquals(vResult, textField.getText().toString());
    }
 
    @Override
    public void tearDown() throws Exception {
        solo.finishOpenedActivities();
    }
 
}

{% endhighlight %}






