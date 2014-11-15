---
layout: post
title: "Android: Testentwicklung mit JUnit"
---



## Hintergrund

Es gibt vielzählige Tools und Möglichkeiten, Apps unter Android zu testen. Eine Möglichkeit ist dabei schlichtweg JUnit zu verwenden. Dies klappt solange ganz gut, solange keine Android API Aufrufe in der zu testenden Klasse stattfinden.

### Im Detail

Als Beispiel wird eine einfache, zu testende Activity namens MainActivity angelegt, die lediglich einen Button bereitstellt und bei Click den Text einer TextView ändert:

{% highlight java %} 
package com.app.jnuit;
 
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
 
public class MainActivity extends Activity {
 
    private Button m_btnSubmit;
    private TextView m_tvSample;
     
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
         
        m_btnSubmit = (Button)findViewById(R.id.btnSubmit);
        m_tvSample = (TextView)findViewById(R.id.txtView1);
         
        m_btnSubmit.setOnClickListener(new View.OnClickListener() {
             
            @Override
            public void onClick(View v) {
                m_tvSample.setText("Button geklickt");
                finish();
            }
        });
    }
}
{% endhighlight %}

Mit Rechtsklick auf das existierende Android Projekt, Auswahl von Tools -> New Test Project, lässt sich ein neues Test-Projekt erstellen.

Mit JUnit-Mitteln gilt es nun, den Klick auf den Button zu erkennen und anschließend den Text der TextView auf korrekte Veränderung zu prüfen:

{% highlight java %} 
package com.app.jnuit.test;
 
import android.content.Intent;
import android.test.suitebuilder.annotation.MediumTest;
import android.widget.Button;
import android.widget.TextView;
 
import com.app.jnuit.MainActivity;
 
public class MyTestCase extends android.test.ActivityUnitTestCase<MainActivity> {
 
    private Intent mStartIntent;
 
    public MyTestCase() {
        super(MainActivity.class);
    }
 
    @Override
    protected void setUp() throws Exception {
        super.setUp();
        mStartIntent = new Intent(Intent.ACTION_MAIN);
    }
 
    @Override
    protected void tearDown() throws Exception {
        super.tearDown();
 
    }
 
    @MediumTest
    public void testButtonClick() {
 
        startActivity(mStartIntent, null, null);
        TextView mTestMessage = (TextView)getActivity().findViewById(com.app.jnuit.R.id.txtView1);
        Button mTestButton = (Button) getActivity().findViewById(com.app.jnuit.R.id.btnSubmit);
         
        assertNotNull(getActivity());
        assertNotNull(mTestButton);
        assertNotNull(mTestMessage);
 
        // Button-Click simulieren
        mTestButton.performClick();
        final String msg = mTestMessage.getText().toString();
         
        //Prüfen, ob sich der Text korrekt geändert hat
        if(msg.equalsIgnoreCase("Button geklickt")){
 
            assertTrue("Button geklickt, true);
             
            //Prüfen ob die Activity beendet wurde
            assertTrue(isFinishCalled());
        }
                 
    }
 
}

{% endhighlight %}