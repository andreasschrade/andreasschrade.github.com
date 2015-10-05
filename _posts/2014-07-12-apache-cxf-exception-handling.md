---
layout: post
title: "Apache CXF - JAX-RS Exception-Handling"
category: "android"
---



## Hintergrund

Dieser Artikel beschreibt die Erstellung eines einfachen Exception-Handlers für Rest-Service, die auf Basis von Apache CXF und JAX-RS (=JSR-311) vorgenommen sind.

### Implementierung
Es ist essentiell darauf zu achten, dass der Aufrufer einer REST-Schnittstelle immer einen Response erhält. Gerade mit auftreten von Exceptions ist es daher eine wichtige Aufgabe, Fehlerfälle möglichst passend und aussagekräftig an den Aufrufer zu reichen, damit dieser die Möglichkeit besitzt, entsprechend darauf zu reagieren.

Zur Realisierung des Fehlerhandlings gilt es zuerst eine individuelle Exception-Handler Klasse anzulegen, die das Interface <em>ExceptionMapper<T></em> implementiert.

{% highlight java %}
public class ExceptionHandler implements ExceptionMapper<Exception>{

	@Override
	public Response toResponse(Exception ex) {
	
	}
	
}
{% endhighlight %}

In diesem Fall wurde als generischer Typ <em>Exception</em> herangezogen. Dies bedeutet, dass dieser Exception-Handler für alle auftretenden Exceptions vom Typ <em>Exception</em> verantwortlich ist.

Damit der Handler Berücksichtigung findet, gilt es diesem den zugehörigern JAX-RS-Server als Provider mitzuteilen.

{% highlight xml %}
<jaxrs:server id="myService" address="/myService">
    <jaxrs:serviceBeans>
        ...
    </jaxrs:serviceBeans>
    <jaxrs:providers>
      <ref bean="exceptionHandler" />
	</jaxrs:providers>
</jaxrs:server>

<bean id="exceptionHandler" class="de.example.ExceptionHandler" />
{% endhighlight %}

Nun lässt sich in der bereits angelegten Klasse die Fehlerbehandlung mit Implementierung von <em>toResponse</em> konfigurieren.

Grundsätzlich empfiehlt es sich, eigene  Exception-Typen für Fehlerfälle vorzusehen damit die Fehlerbehandlungslogik differenziert nach Fehlertyp einen zugehörigen Response mitsamt HTTP-Statuscodes erzeugen kann.
Denkbar wäre etwa, einen Basis-Exceptiontypen anzulegen, von dem wiederum Exceptiontypen erben, die jeweils für einen Serverfehler und einen Clientfehler stehen.

Beispiel:

{% highlight java %}
  // Allgemeine Exception die für Fehler innerhalb der Request-Verarbeitung stehen
  public class ServiceException extends RuntimeException {
    // ... 
  }
  
  // Exceptions die im Zusammenhang mit fehlerhaften Aufrufen von seiten des Clients stehen -> HTTP: 4XX
  public class ClientServiceException extends ServiceException {
    private ValidationError validationError; // Enthält Informationen zum fehlerhaften Aufruf -> Validierungsinformationen
    // ... 
    @Override
  	public String toString() {
	   	return "Ungültiger Aufruf: " + validationError;
	 }
  }
  
  // Exceptions die im Zusammenhang mit fehlerhafter Programmlogik stehen -> HTTP: 5XX
  public class ServerServiceException extends ServiceException {
    // ...
    @Override
  	public String toString() {
	   	return "Technischer Fehler mit Verarbeitung der Anfrage: " + getMessage();
	 } 
  }
  
{% endhighlight %}

Durch Trennung der Fehlerarten in einzelne Exception-Typen lassen sich diese entweder in einem Exception-Handler abbilden ...

{% highlight java %}
/* Exception-Handler behandelt alle Fehlerfälle/Exceptions */
public class ExceptionHandler implements ExceptionMapper<Exception>{

	@Override
	public Response toResponse(Exception ex) {
		MyServiceResponse response = new MyServiceResponse();
		
		int httpCode = 500;
		
	  if(ex instanceof ClientServiceException) {
      httpCode = 400; 
    } 	
		
		response.setErrorMessage(ex);
		
		return Response.status(httpCode).entity(response).build();;
	}
}  
{% endhighlight %}

... oder, besser wäre es, für jeden Exception-Typ einen eigenen Exception-Handler zu definieren.

Sicherlich, das obig dargestellte Beispiel gilt es  lediglich als einfache Möglichkeit zu betrachten, wie ein Exception-Handling aussehen kann. Essentiell ist die korrekte Vergabe der HTTP-Codes im Response: Diese sollten je nach konkretem Fehlerfall (z.B. Ungültige Credentials, Fehlende Pflichtangabe) gesetzt werden.

Um das Mapping zwischen Fehlerfall, Fehlercode und Http-Code abzubilden, wäre etwa der Einsatz eines Fehler-Enums denkbar:

{% highlight java %}
public enum RestError {
	ACCESS_DENIED("AccessDenied", 403,"Access Denied"),
	QUOTA_EXCEEDED("QuotaExceeded", 403,"The monthly quota has been exceeded!"),
	INTERNAL_ERROR("InternalError", 500, "An unexpected error occured. Please try again."),
	INVALID_REQUEST("InvalidRequest", 400, "Missing or invalid parameters passed!");
	
	private String errorKey;
	private int httpCode;
	private String errorDescription;
	
	private RestError(String errorKey, int httpCode, String errorDescription) {
		this.errorKey = errorKey;
		this.httpCode = httpCode;
		this.errorDescription = errorDescription;
	}
	
{% endhighlight %}

Erwartet nun der eigene Exception-Typ ein Objekt vom Typ <em>RestError</em>, lässt sich das Mapping im Exception-Handler vollkommen generisch abbilden.
