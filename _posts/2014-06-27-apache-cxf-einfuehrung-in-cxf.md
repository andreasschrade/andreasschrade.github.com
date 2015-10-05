---
layout: post
title: "Apache CXF - Erstellung eines REST-Services"
category: "java"
---



## Hintergrund

Dieser Artikel beschreibt die Erstellung eines einfachen REST-Services auf Basic von Apache CXF, JAX-RS und Spring.
Zudem wird Maven als Build-Tool und Java 7 eingesetzt.

### Implementierung

Gegentand des Beispiel-Services ist ein einfacher HTTP-GET REST-Service, welcher anhand der ISBN-Nummber detaillierte Informationen zu einem Buch liefert.

Zuerst gilt es einmal alle erforderlichen Abhängigkeiten in der pom.xml aufzunehmen. Darunter befinden sich sowohl die Abhängingkeiten zu Spring, als auch zu Apache CXF:

{% highlight xml %}
<!-- CXF dependencies -->
<dependency>
     <groupId>org.apache.cxf</groupId>
     <artifactId>cxf-bundle-jaxrs</artifactId>
     <version>2.7.11</version>
</dependency>

<dependency>
  <groupId>org.apache.cxf</groupId>
  <artifactId>cxf-rt-transports-http</artifactId>
  <version>2.7.11</version>
</dependency>

<!-- Spring dependencies -->

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>3.2.8.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>3.2.8.RELEASE</version>
</dependency>
 <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>3.2.8.RELEASE</version>
</dependency>

{% endhighlight %}

Als nächsten Schritt muss die web.xml (webapp/WEB-INF/web.xml) um das Laden des Spring-Context, sowie des CXF-Servlet ergänzt werden:

{% highlight xml %}
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath*:/spring/context.xml</param-value>
	</context-param>

	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<servlet>
		<servlet-name>CXFServlet</servlet-name>
		<servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>
	  <load-on-startup>1</load-on-startup>
	</servlet>

 	<servlet-mapping>
		<servlet-name>CXFServlet</servlet-name>
		<url-pattern>/my-service/*</url-pattern>
	</servlet-mapping>
{% endhighlight %}

Im Servlet-Mapping des CXF-Servlet lässt sich unter <em>url-pattern</em> ein relativer URL-Bestandteil angeben, der für alle Service als Basis-URL gesetzt wird.
(z.B. localhost:8080/my-service/book/)

Die Spring-Context-Konfigurationsdatei (context.xml) erhält nun als nächsten Schritt die Definition des Bücher-Services:

{% highlight xml %}
...

<bean id="bookServiceController" class="de.example.BookServiceController" />

<jaxrs:server id="bookService" address="/v1">
  <jaxrs:serviceBeans>
      <ref bean="bookServiceController"/>
  </jaxrs:serviceBeans>
</jaxrs:server>
   	
..
{% endhighlight %}

In der Spring-Konfiguration wird zuerst eine Bean namens "bookServiceController" vom Typ "BookServiceController" erzeugt. Die zugehörige Implementierung dieser Klasse stellt später den Einstiegspunkt für jegliche Requests dar, die zum konfigurierten URL-Schema unter "address" passen.
<em>bookService</em> stellt letztlich den JAX-RS Server dar. Unter <em>address</em> wurde im Beispiel lediglich die Versionierung vorgenommen. Sollen mehrere unterschiedliche Service gleichzeitig betrieben werden, muss bei diesem Attribut ein unterscheidendes Merkmal hinzugefügt werden.

Entsprechend gilt es in der Spring-Konfiguration folgende Namespace-Informationen zu hinterlegen:

{% highlight xml %}
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:jaxrs="http://cxf.apache.org/jaxrs"
       xsi:schemaLocation="http://cxf.apache.org/jaxrs http://cxf.apache.org/schemas/jaxrs.xsd">
{% endhighlight %}

Als nächstes gilt es das Response-Objekt des Methodenaufrufes anzulegen bzw. mit entsprechenden Annotations zu versehen:

{% highlight java %}
@XmlAccessorType(XmlAccessType.FIELD)
@XmlType(name = "", propOrder = {
    "title",
    "author"
})
@XmlRootElement(name = "book")
public class Book {

    @XmlElement(name = "Title")
    protected String title;
    
    @XmlElement(name = "Author")
    protected String author;
    
    // .. getter & setter
{% endhighlight %}

In größeren Projekten empfiehlt es sich, Request- und Response-Objekte auf Basis einer WADL sowie einem XSD-Schema zur Build-Zeit generieren zu lassen.
Dies erspart die mühsame Anlage derartiger Klassen und zudem ist die Basis für eine clientseitige Implementierung geschaffen.

Nun gilt es zuletzt noch die eigentliche Endpoint bzw. Controller-Implementierung vorzunehmen:

{% highlight java %}
@Path("/book")
public class BookStoreService {
 
	@GET
	@Produces(MediaType.APPLICATION_JSON)
	public Response getAll() {
		List<Book> books = bookService.getAll();
		GenericEntity<List<Book>> bookWrapper = new GenericEntity<List<Book>>(
				books) {
		};
		return Response.ok(bookWrapper).build();
	}
 
	@GET
	@Path("/{isbn}")
	@Produces(MediaType.APPLICATION_JSON)
	public Response getByIsbn(final @PathParam("isbn") String isbn) {
		Book book = bookService.getById(isbn);
		return Response.ok(book).build();
	}
}
{% endhighlight %}

Die Path-Annotation auf Klassenebene erlaubt nun nochmal, sich auf einen relativen URL-Bestandteil zu beziehen unter der Requests behandelt werden.
Desweiteren sind zwei Methoden jeweils mit der <em>@GET</em>-Annotation versehen. Diese besagt lediglich aus, dass die Methode im Falle eines HTTP-GET Requests, also unter Verwendung der HTTP-Methode GET eine Rolle spielt.
Optional lässt sich zudem mit der <em>@Path</em>-Annotation ein weiterer relativer ergänzender Pfad angegeben. Dieser kann unter Verwendung geschweifter Klammern Platzhalter von Parametern aufnehmen.

Beispielsweise bezweckt die Angabe "/{isbn}" das Mapping eines beliebigen Parameters auf die Methodenvariable <em>isbn</em>.  Mit Angabe von <em>@Produces</em> lässt sich die Repräsentation des Responses angegeben.
Im Beispiel wurde sich für JSON als Datenrepräsentation entschieden. Standardmäßig, wenn keine explizite Angabe erfolgt, wird ein XML dem Aufrufer zurückgegeben. Es ist zudem möglich, beide Response-Typen parallel zu verwenden.
Welche Repräsentation vom Aufrufer gewünscht wird, kann im Accept-Header vom Aufrufer angegeben werden.

In Kombination mit den weiteren URL-Angaben in der web.xml sowie Spring-Konfiguration lässt sich somit der Service nach Deployment in einem Servlet-Container wie folgt aufrufen:

1. Zur Ermittlung aller Bücher  <br>
localhost:8080/my-service/v1/book

2. Zur Ermittlung eines Buches anhand ISBN-Nummer  <br>
localhost:8080/my-service/v1/book/3871347108

