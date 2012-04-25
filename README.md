## [SwaggerSocket](https://github.com/wordnik/swagger-sockets/wiki/Swagger-Socket-Protocol): A REST over WebSocket Protocol

The SwaggerSocket protocol allows any existing REST Resource to be executed on top of the WebSocket Protocol. Resources can be deployed as it is, without any modification and take advantage of the SwaggerSocket protocol.

You can also add bi-directional support to your REST resource by extending it using the [Atmosphere Framework](http://github.com/Atmosphere/atmosphere).

### Download SwaggerSocket

Using Maven or SBT

```xml
    <!-- Server side -->
    <dependency>
       <groupId>com.wordnik</groupId>
       <artifactId>swaggersocket</artifactId>
       <version>1.0.0</version>
    </dependency>
    <!-- Client side --> 
     <dependency>
       <groupId>com.wordnik</groupId>
       <artifactId>swaggersocket.js</artifactId>
       <version>1.0.0</version>
       <type>war</type>
    </dependency>   
```

Manual download [here](http://search.maven.org/#search|ga|1|swaggersocket)

### Getting started using the samples
The quickest way to see how the protocol works is to try the samples. You can download them from [here](http://search.maven.org/#search|ga|1|swaggersocket). Just do

```bash
  % unzip swaggersocket-{sample_name}-distribution
  % chmod a+x ./bin/nettosphere.sh
  % ./bin/nettosphere.sh
```

You can also build the sample yourself

```bash
  % git clone git@github.com:wordnik/swagger-sockets.git
  % cd swagger-socket
  % mvn 
  % cd samples/swaggersocket-echo OR samples/swaggersocket-twitter
  % mvn jetty:run
```

or take a look at [HelloWorld](https://github.com/wordnik/swagger-sockets/wiki/Getting-started-with-SwaggerSocket-and-Jersey) mini tutorial. 
You can also look at our samples :

 * Twitter's Real Time Search [client code](https://github.com/wordnik/swagger-sockets/blob/master/samples/swaggersocket-twitter/src/main/webapp/index.html#L10) | [server code](https://github.com/wordnik/swagger-sockets/blob/master/samples/swaggersocket-twitter/src/main/java/com/wordnik/swaggersocket/samples/TwitterFeed.java#L45) | [download sample](http://search.maven.org/remotecontent?filepath=com/wordnik/swaggersocket-twitter/1.0.0/swaggersocket-twitter-1.0.0-distribution.zip)
 * Wordnik's Real Time Search [client code](https://github.com/wordnik/swagger-sockets/blob/master/samples/swaggersocket-wordnik/src/main/webapp/index.html#L10) | [server code](https://github.com/wordnik/swagger-sockets/blob/master/samples/swaggersocket-wordnik/src/main/scala/com/wordnik/swaggersocket/samples/WordnikResourceProxy.scala#L30) | [download sample](http://search.maven.org/remotecontent?filepath=com/wordnik/swaggersocket-wordnik/1.0.0/swaggersocket-wordnik-1.0.0-distribution.zip)
 * Simple Swagger Sockets Protocol Echo [client code](https://github.com/wordnik/swagger-sockets/blob/master/samples/swaggersocket-echo/src/main/webapp/index.html#L9) | [server code](https://github.com/wordnik/swagger-sockets/blob/master/samples/swaggersocket-echo/src/main/scala/org/wordnik/swaggersocket/samples/SwaggerSocketResource.scala#L16) | [download sample](http://search.maven.org/remotecontent?filepath=com/wordnik/swaggersocket-echo/1.0.0/swaggersocket-echo-1.0.0-distribution.zip)

### SwaggerSocket Protocol Server Implementation
To enable SwaggerSocket, add the following in your web.xml. Currently, SwaggerSocket only supports [Jersey](http://jersey.java.net/) for REST Resources (other implementations like RestEasy and RESTLet are coming).  

```xml
    <servlet>
        <description>SwaggerSocketServlet</description>
        <servlet-name>SwaggerSocketServlet</servlet-name>
        <servlet-class>com.wordnik.swaggersocket.server.SwaggerSocketServlet</servlet-class>
        <load-on-startup>0</load-on-startup>
    </servlet>
```

### SwaggerSocket JavaScript API
The SwaggerSocket Client is defined as

```javascript
    var swaggerSocket = new jQuery.swaggersocket.SwaggerSocket();
```

#### Listeners are per Request. The responses will be delivered as they come and can be correlated with their associated request using response.getRequest().

```javascript
    var ss = new jQuery.swaggersocket.SwaggerSocketListener();
    ss.onOpen = function(response) {};
    ss.onClose = function(Response) {}; // Called when the Websocket gets closed
    ss.onError = function(Response) {}; // When an error occurs
    ss.onResponse = function(Response) {}; // When a response is ready
    ss.onResponses = function (Response) {}; // A List of all the ready responses
```

#### Opening a connection

```javascript
     var request = new jQuery.swaggersocket.Request()
           .path(document.location.toString())
           .listener(ss);
     swaggerSocket.open(request);
```

#### Sending requests -- You can send an array of Requests or single Request.

```javascript
    var requests = new Array();
    requests[0] = new jQuery.swaggersocket.Request()
            .path("path1")
            .method("POST")
            .data("FOO")
            .listener(ss);
    requests[1] = new jQuery.swaggersocket.Request()
            .path("/path2")
            .method("POST")
            .data("BAR")
            .listener(ss);
    swaggerSocket.send(requests);
```

### SwaggerSocket Scala API
The SwaggerSocket protocol can also be used using the Scala language. The SwaggerSocket Scala library is using Asynchronous I/O so all the API calls are non blocking. First, you need to hanshake using

```scala
    val ss = SwaggerSocket().open(new Request.Builder().path(getTargetUrl + "/").build())
```

Then you are ready to start sending requests. As simple as:

```scala
    // send (Request, SwaggerSoketListener )
    // or send (Array[Request], SwaggerSoketListener)
    ss.send(new Request.Builder()
      .path("/b")
      .method("POST")
      .body("Yo!")
      .format("JSON")
      .build(), new SwaggerSocketListener() {
      	override def error(e: SwaggerSocketException) {
           // Invoked when an exception occurs
	    }
     	override def message(r: Request, s: Response) {
           // Response is delievered here
        }
      })
```
Once completed, you just need to close

```scala
    ss.close
```

### How the protocol works
To read more about how the protocol works, take a look at the [SwaggerSocket Protocol Specification](https://github.com/wordnik/swagger-sockets/wiki/Swagger-Socket-Protocol)

