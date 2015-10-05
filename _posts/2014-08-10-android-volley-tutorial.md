---
layout: post
title: "Android: Volley Tutorial"
category: "android"
---



## Hintergrund

Google hat kürzlich eine neue Library namens Android Volley vorgestellt, die zum Ziel hat, auf möglichst einfache Weise effektive Netzwerkoperationen durchzuführen.


### Setup
                                                             
Das Setup ist recht einfach. Es muss lediglich das <a href="https://android.googlesource.com/platform/frameworks/volley">Volley Projekt geklont</a> werden um anschließend in ein Projekt kopiert zu werden.

### Schlüsselklassen

Folgend eine Auflistung der essentiellen Klassen von Android Volley mit deren Bedeutung:

- RequestQueue: Eine Queue, die alle zu tätigenden HTTP-Requests aufnimmt
- Request: Basisklasse, die netzwerkrelevante Informationen wie HTTP-Methoden aufnimmt
- StringRequest: HTTP Request, bei dem der Response als String geparsed wird
- JsonObjectRequest: HTTP Request, bei dem der Response ein JSONObject ist.

### Erste Schritte

<strong>Vorbereitung</strong>

Zuerst gilt es eine RequestQueue zu erzeugen, die idealerweise in der gesamten Applikation lediglich einmal existiert:

{% highlight java %}
RequestQueue queue = Volley.newRequestQueue(myContext);
{% endhighlight %}

<strong>GET-Requests</strong>

GET-Requests lassen sich mit Volley sehr einfach durchführen. Das folgige Beispiel verwendet ein JsonObjectRequest, welches folgende vier Parameter erwartet:

- Http-Methode
- Url
- Json
- Response-Listener (enthält Implementierung für Sucess- und Error-Listener)

{% highlight java %}
final String url = "http://httpbin.org/get?param1=test";

// 
JsonObjectRequest getRequest = new JsonObjectRequest(Request.Method.GET, url, null,
	new Response.Listener<JSONObject>() 
	{
		@Override
		public void onResponse(JSONObject response) {	
                        // display response		
			Log.d("Response", response.toString());
		}
	}, 
	new Response.ErrorListener() 
	{
		 @Override
		 public void onErrorResponse(VolleyError error) {			 
			Log.d("Error.Response", response);
	   }
	}
);

// Zur Request-Queue hinzufügen
queue.add(getRequest);
{% endhighlight %}

<strong>Post-Requests</strong>

Bei Post-Requests gilt es die Methode getParams zu überschreiben. Diese erwartet als Rückgabe eine Map gefüllt mit Key-Value Werten, die in den Request aufgenommen werden sollen:

{% highlight java %}
url = "http://httpbin.org/post";
StringRequest postRequest = new StringRequest(Request.Method.POST, url, 
	new Response.Listener<String>() 
	{
		@Override
		public void onResponse(String response) {
			// response
			Log.d("Response", response);
		}
	}, 
	new Response.ErrorListener() 
	{
		 @Override
		 public void onErrorResponse(VolleyError error) {
			 // error
			 Log.d("Error.Response", response);
	   }
	}
) {		
	@Override
	protected Map<String, String> getParams() 
	{  
			Map<String, String>  params = new HashMap<String, String>();  
			params.put("user", "42");  
			params.put("name", "Bruce");
			
			return params;  
	}
};
queue.add(postRequest);
{% endhighlight %}

<strong>Put-Requests</strong>

Put-Requests verhalten sich prinzipiell analog dem Post-Request:

{% highlight java %}
url = "http://httpbin.org/put";
StringRequest putRequest = new StringRequest(Request.Method.PUT, url, 
	new Response.Listener<String>() 
	{
		@Override
		public void onResponse(String response) {
			// response
			Log.d("Response", response);
		}
	}, 
	new Response.ErrorListener() 
	{
		 @Override
		 public void onErrorResponse(VolleyError error) {
                         // error
			 Log.d("Error.Response", response);
	   }
	}
) {

	@Override
	protected Map<String, String> getParams() 
	{  
			Map<String, String>  params = new HashMap<String, String> ();  
			params.put("user", "42");  
			params.put("name", "Bruce");
			
			return params;  
	}

};

queue.add(putRequest);
{% endhighlight %}

<strong>Delete-Requests</strong>

Delete-Requests sind denkbar einfach. Neben der zu löschenden Ressource wird keinerlei weitere Angabe benötigt:

{% highlight java %}
url = "http://httpbin.org/delete";
StringRequest dr = new StringRequest(Request.Method.DELETE, url, 
	new Response.Listener<String>() 
	{
		@Override
		public void onResponse(String response) {
			// response
			Toast.makeText($this, response, Toast.LENGTH_LONG).show();
		}
	}, 
	new Response.ErrorListener() 
	{
		 @Override
		 public void onErrorResponse(VolleyError error) {
			 // error.
			 
	   }
	}
);
queue.add(dr);
{% endhighlight %}

<strong>Request-Priorisierung</strong>

Erfordert die Anwendung die parallele Verarbeitung mehrerer Request, so kann es sinnvoll sein, diese entsprechend deren Bedeutung zu priorisieren.
Dank der Anpassbarkeit von Volley gelingt dieses Ziel recht schnell mit Erstellung einer eigenen Request-Klasse:

{% highlight java %}
public class CustomStringRequest extends StringRequest {
 private Priority priority = Priority.LOW;

  CustomStringRequest(String url, Listener<string>listener,ErrorListener errorListener) {
    super(url, listener, errorListener);
  }

  @Override
  public Priority getPriority(){
    return priority;
 }

 public void setPriority(Priority priority){
    this.priority = priority;
 }
}
{% endhighlight %}

Per Default wird die Priorität auf "Low" gesetzt. Diese lässt sich mit Aufruf der setPriority-Methode anpassen:

{% highlight java %}
Request.setPriority(Priority.IMMEDIATE)
{% endhighlight %}
