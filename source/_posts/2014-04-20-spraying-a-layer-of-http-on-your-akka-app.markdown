---
layout: post
title: "Spray a layer of HTTP on your Akka App"
date: 2014-04-20 23:36:33 -0400
comments: true
categories: spray akka http
published: false
---
Often times, my Akka projects have a need for a lightweight HTTP API layer. Instead of incurring the overhead of converting the app into a full blown [Play](http://www.playframework.com/) app, [Spray-Can](http://spray.io/introduction/what-is-spray/) seems to offer an interesting lightweight option. I've mocked up a [sample project](https://github.com/sqldump/akka-spray) with an ActorSystem and a simple Spray backed HTTP API.

####Installation
Adding Spray to the [Akka Skeleton](http://blog.abhinav.ca/blog/2014/04/17/scaffolding-an-akka-project/) involves the following changes:
{% codeblock lang:scala build.sbt %}
resolvers ++= List(
	"Spray Repo" at "http://repo.spray.io"
)
{% endcodeblock %}
{% codeblock lang:scala project/Dependencies.scala %}
object Library {
  ...
  val sprayCan  = "io.spray" %  "spray-can"  % "1.2.1"
  val sprayJSON = "io.spray" %% "spray-json" % "1.2.6"
}

object Dependencies {
  ...
  sprayCan,
  sprayJSON
}
{% endcodeblock %}

####Setup
Wire up the Main and spawn a StatefulActor
{% codeblock lang:scala StatefulActor.scala %}
// TODO
{% endcodeblock %}

####Usage
Spray-Can works by tapping into Akka's IO layer using the `akka.io.IO` object. When an HTTP request comes in, Spray creates an actor representing that request which then sends out a message of type `spray.can.Http.Connected` and waits for someone to respond with `spray.can.Http.Register(ActorRef)`. Registering means that actor who responded is accepting the responsibility of responding to the request. Any messages of type `HttpResponse` sent to the request actor are forwarded to the client.
  
{% codeblock lang:scala %}
import akka.io.IO
import spray.can.Http
  ...
  
  val handler = system.actorOf(Props[HandlingActor], name="handler")
  val sprayInterface = "0.0.0.0"
  val sprayPort = 8080
  
  IO(Http) ! Http.Bind(handler, interface = sprayInterface, port = sprayPort)
{% endcodeblock %}
  
The `HandlingActor` is used to route requests to other actors. [Spray-Routing](http://spray.io/documentation/1.2.1/spray-routing/) provides a better way of achieving this, but the sytanx is a bit strange at first and it involves adding another module to the dependency list.

{% codeblock lang:scala HandlingActor.scala %}
package org.sqldump.spray.actors

import akka.actor.Actor
import akka.util.Timeout
import scala.concurrent.duration._
import spray.can.Http
import spray.http.{HttpResponse, Uri, HttpRequest}
import spray.http.HttpMethods.GET

class HttpHandler extends Actor {
  implicit val timeout: Timeout = 1.second

  override def receive: Actor.Receive = {
    case _: Http.Connected =>
      sender ! Http.Register(self)

    case HttpRequest(GET, Uri.Path("/"), _, _, _) =>
      sender ! HttpResponse(status=200, entity = "{\"status\": \"ok\"}")

    case _: HttpRequest =>
      sender ! HttpResponse(status=404, entity = "Not Found")
  }
}
{% endcodeblock %}

This `HandlingActor` will respond with an `HTTP 200 { "status": "ok" }` for a `GET /` request. In the `HandlingActor`, you can reply back to the client by simply messaging the `sender`. However, to start forwarding requests for other actors to handle, we must save a reference to the `sender` and include it in the message to any actors down the chain so that they're able to respond to the client. 

You can add additional handlers by including the following cases above the default HTTP 200 case:
  
{% codeblock lang:scala HandlingActor.scala %}
case HttpRequest(GET, Uri.Path(path), _, _, _) if path.equals("/state") =>
  val peer = sender
  Main.statefulActor ! Request(peer)

case HttpRequest(GET, Uri.Path(path), _, _, _) if path.equals("/shutdown") =>
  sender ! HttpResponse(status=200, entity = "{\"result\": \"success\"}")
  Main.statefulActor ! ShutDown
{% endcodeblock %}

