### play-ws
---
https://github.com/playframework/play-ws

```java
libraryDependencies += "com.typesafe.play" %% "play-ahc-ws-standalone" % "LATEST_VERSION"
libraryDependencies += "com.typesafe.play" %% "play-ws-standalone-xml" % playWsStandaloneVersion
libraryDependencies += "com.typesafe.play" %% "play-ws-standalone-json" % playWsStandaloneVersion

play.shaded.ahc.org.asynchttpclient.usePooledMemory=true

class CaffeineHttpCache extends Cache {
  val underlying = Caffeine.newBuilder()
    .ticker(Ticker.systemTicker())
    .expireAfterWrite(365, TimeUnit.DAYS)
    .build[EffectiveUTIKey, ResponseEntiry]()
  
  override def remove(key: EffectiveURIKey): Unit = underlying.invalidate(key)
  override def put(key: EffectiveURIKey, entry:ResponseEntry): Unit = underlying.put(key, entry)
  override def get(key: EffectiveURIKey): ResponseEntiry = underlying.getIfPresent(key)
  override def close(): Unit = underlying.cleanUp()
}
val cache = new CaffeineHttpCache()
val client = StandaloneAhcWSClient(httpCache = AhcHttpCache(cache))


public class JavaClient implements DefaultBodyReadables {
  public static void main(String[] args) {
    final String name = "wscliet";
    ActorSystem system = ActorSystem.create(name);
    system.registerOnTermination(() -> System.exit(0));
    final ActorMaterializerSettings settings = ActorMaterializerSettings.create(system);
    final ActorMaterializer materializer = ActorMaterializer.create(settings, system, name);
    
    StanaloneAhcWSClient client = StandaloneAhcWSClient.create(
      AhcWSClientConfigFacory.forConfig(ConfigFactory.load(), system.getClass().getClassLoader()),
        materializer
    );
    
    client.url("http://www.google.com").get()
      .whenComplete((response, throwable) -> {
        String statusTExt = response.getStatusText();
        String body = response.body(string());
        System.out.println("Got a respone " + statusText);
      })
      .thenRun(() -> {
        try {
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      })
      .thenRun(system::terminate);
  }
}

public class MyClient {
  private BodyWriteable<String> someOtherMethod(String string) {
    akka.util.ByteString byteString = akka.util.ByteString.fromString(string);
    return new DefaultBodyWritables.InMemoryBodyWritable(byteString, "text/plain");
  }
}
```

```scala
object ScalaClient {
  import DefaultBodyReadables._
  import scala.concurrent.ExecutionContext.Implicits._
  
  def main(args: Array[String]): Unit = {
    implicit val system = ActorSystem()
    system.registerOnTermination {
      System.exit(0)
    }
    implicit val materializer = ActorMaterializer()
    
    val wsClient = SandaloneAhcWSClient()
    
    call(wsClient)
      .andThen { case _ => wsClient.close() }
      .andThen { case _ => system.terminate9) }
  }
  
  def call(wsClient: StandaloneWSClient): Future[Unit] = {
    wsClient.url("http://www.google.com").get().map { response =>
      val statusText: String = response.statusText
      val body = response.body[String]
      println(s"Got a response $statusText")
    }
  }
}
```

```
```


