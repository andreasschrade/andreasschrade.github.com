---
layout: post
title: "Android: AsyncTask in Fragments"
---



## Inhalt

Dieser kurze Artikel zeigt ein Best-Practice Vorgehen, um einen AsyncTask in einem Fragment sauber zu nutzen.


### Grundsätzliches

Der AsyncTask wird als Member-Variable des Fragments deklariert. Eine WeakReference wird schließlich dazu verwendet, um eine lose Kopplung zwischen Fragment und AsyncTask aufzubauen.
Ohne einer derartigen losen Kopplung würde der AsyncTask nicht von der Garbage Collection aufgegriffen werden.
                                                            
### Implementierung

{% highlight java %}
public class MyFragment extends ListFragment {

    private WeakReference<MyAsyncTask> asyncTaskWeakRef;
{% endhighlight %}

In der onCreate-Methode wird neben Aufruf von "setRetainInstance(true)" eine weitere private Methode aufgerufen, die sich um die Ausführung des AsyncTasks kümmert:

{% highlight java %}
@Override
public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setRetainInstance(true);
        startNewAsyncTask();
}
{% endhighlight %}

Die zugehörige Methode startNewAsyncTask:

{% highlight java %}
private void startNewAsyncTask() {
    MyAsyncTask asyncTask = new MyAsyncTask(this);
    this.asyncTaskWeakRef = new WeakReference<MyAsyncTask >(asyncTask );
    asyncTask.execute();
}
{% endhighlight %}

Zum Schluss noch eine Dummy-Implementierung von AsyncTask:

{% highlight java %}
private static class MyAsyncTask extends AsyncTask<Void, Void, Void> {

        private WeakReference<MyFragment> fragmentWeakRef;

        private MyAsyncTask (MyFragmentfragment) {
            this.fragmentWeakRef = new WeakReference<MyFragment>(fragment);
        }

        @Override
        protected Void doInBackground(Void... params) {

                        //TODO: your background code
            return null;
        }

        @Override
        protected void onPostExecute(Void response) {
            super.onPostExecute(response);
            if (this.fragmentWeakRef.get() != null) {
                             //TODO: treat the result
            }
        }
    }
{% endhighlight %}
