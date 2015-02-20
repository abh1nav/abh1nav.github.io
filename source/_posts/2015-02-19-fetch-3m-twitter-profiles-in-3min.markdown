---
layout: post
title: "Fetch 3M Twitter Profiles in 3min"
date: 2015-02-19 21:15:09 -0500
comments: true
categories: akka scala streams reactive
---

During these past few weeks, I've had a chance to play around quite a bit with the soon-to-be-1.0 [Akka Streams](http://doc.akka.io/docs/akka-stream-and-http-experimental/1.0-M3/scala.html) module and I've been looking for a concrete reason to write about why this is an important milestone for the stream processing ecosystem. It just so happens that this week, we were conducting an experiment related to Twitter follower analysis that required pulling a few million public user profiles of followers of large Twitter accounts and the ability to do so in a relatively short amount of time. I decided to use Akka Streams for this project to see how far I could push it in a real world scenario.

### App Skeleton

I decided to start with a regular Scala command line app and hardcode the Twitter ID of the person whose followers profiles we were attempting to retrieve (since this is going to be throwaway code just for the sake of the experiment). The only dependencies were [akka-actor](http://mvnrepository.com/artifact/com.typesafe.akka/akka-actor_2.11/2.3.9), [akka-stream-experimental](http://mvnrepository.com/artifact/com.typesafe.akka/akka-stream-experimental_2.11/1.0-M3) and [twitter4j-core](http://mvnrepository.com/artifact/org.twitter4j/twitter4j-core/4.0.2).

```scala
object Main extends App {

  // ActorSystem & thread pools
  implicit val system: ActorSystem = ActorSystem("centaur")
  val executorService: ExecutorService = Executors.newCachedThreadPool()
  val ec: ExecutionContext = ExecutionContext.fromExecutorService(executorService)
  val log: LoggingAdapter = Logging.getLogger(system, Main)
  implicit val materializer = ActorFlowMaterializer()(system)

  val userId = 410939902L // @headinthebox ~12K followers
  
  // Do stuff
}
```

At this point, we have a working app that can be invoked using `sbt run`. The next steps were to get the Twitter4j interactions out of the way so we can focus on the stream processing. We need two important helpers: f

1) Fetch all follower IDs of a given userID 
2) Fetch a user profile given a userID.

#### Calling the Twitter API

Any call made to Twiter's API needs an authenticated Twitter client. Given a set of credentials, a `twitter4j.Twitter` object is constructed as follows:

```scala
import twitter4j.{Twitter, TwitterFactory}
import twitter4j.auth.AccessToken
import twitter4j.conf.ConfigurationBuilder

object TwitterClient {
    // Fill these in with your own credentials
    val appKey: String = ""
    val appSecret: String = ""
    val accessToken: String = ""
    val accessTokenSecret: String = ""

    def makeClient(appKey: String, appSecret: String, accessToken: String, accessTokenSecret: String): Twitter = {
        val factoryBuilder = new ConfigurationBuilder()
            factoryBuilder.setJSONStoreEnabled(true)
            factoryBuilder.setIncludeMyRetweetEnabled(true)
            factoryBuilder.setIncludeEntitiesEnabled(true)
        val factory = new TwitterFactory(factoryBuilder.build())

        val t = factory.getInstance()
        t.setOAuthConsumer(appKey, appSecret)
        t.setOAuthAccessToken(new AccessToken(accessToken, accessTokenSecret))  
        t
    }    
}

```

#### Given a user, return all followers

Using the above `makeClient` method, we can fetch a user's profile and list of followers with 

```scala
object TwitterHelpers {
    // Lookup user profiles in batches of 100
    def lookupUsers(ids: List[Long]): List[User] = {
      Main.log.info(s"Looking up ${ids.size} users")
      val client = pool.getClient()
      val res = client.lookupUsers(ids.toArray)
      res.toList
    }

    // Fetch the IDs of a user's followers
    def getFollowers(userId: Long): Try[Set[Long]] = {
      Try({
        val followerIds = mutable.Set[Long]()
        var cursor = -1L
        do {
          val client = TwitterClient.makeClient(appKey, appSecret, accessToken, accessTokenSecret)
          val res = client.friendsFollowers().getFollowersIDs(userId, cursor, 5000)
          res.getIDs.toList.foreach(x => followerIds.add(x))
          if (res.hasNext) {
            cursor = res.getNextCursor
          }
        } while (cursor > 0)
        followerIds.toSet
      })
    }
}
```








