<!-- .slide: class="cover" -->
# Akka Typed
Pour que le typage fort ne reste pas lettre morte

---

Cyrille Chépélov — Sylvain Veyrié

Transparency Rights Management

> Scala, droits d'auteur, musique

---

## Akka ?

___

### Les systèmes à acteurs (1973)

* Un acteur est une unité de traitement qui peut :
<!-- .element: class="fragment" data-fragment-index="1" -->
 * Recevoir et traiter des messages un par un
<!-- .element: class="fragment" data-fragment-index="2" -->
 * Envoyer des messages à d’autres acteurs
<!-- .element: class="fragment" data-fragment-index="3" -->
 * Contenir un état, effectuer des entrées/sorties
<!-- .element: class="fragment" data-fragment-index="4" -->
* Messages immutables et indépendants
<!-- .element: class="fragment" data-fragment-index="5" -->
* Acteurs mutables et indépendants
<!-- .element: class="fragment" data-fragment-index="6" -->

___

### Pourquoi c’est bien

* Fondamentalement scalable
<!-- .element: class="fragment" data-fragment-index="1" -->
 * threads, clusters, etc. pris en charge
<!-- .element: class="fragment" data-fragment-index="1" -->
* Le seul contrat est le message
<!-- .element: class="fragment" data-fragment-index="2" -->
 * Découplage
<!-- .element: class="fragment" data-fragment-index="2" -->
 * Facilement testable
<!-- .element: class="fragment" data-fragment-index="2" -->
 * Cycle de vie indépendant pour chaque acteur
<!-- .element: class="fragment" data-fragment-index="2" -->
* Parfait pour :
<!-- .element: class="fragment" data-fragment-index="3" -->
 * traitements asynchrones
<!-- .element: class="fragment" data-fragment-index="3" -->
 * flux de données
<!-- .element: class="fragment" data-fragment-index="3" -->

note: bien dire que Acteur == Thread (FIXME)

___

### Akka (2010)

* Système d’acteurs en Scala (Apache License 2)
<!-- .element: class="fragment" data-fragment-index="1" -->
* Scala et Java (+ versions .NET et JS)
<!-- .element: class="fragment" data-fragment-index="2" -->
* Play! Framework 2 réécrit avec Akka
<!-- .element: class="fragment" data-fragment-index="3" -->
* Désormais partie de la stack Typesafe/Lightbend
<!-- .element: class="fragment" data-fragment-index="4" -->
* Modules :
<!-- .element: class="fragment" data-fragment-index="5" -->
 * Akka Stream (flux avec backpressure)
<!-- .element: class="fragment" data-fragment-index="5" -->
 * Akka persistence (event sourcing)
<!-- .element: class="fragment" data-fragment-index="5" -->
 * Akka HTTP
<!-- .element: class="fragment" data-fragment-index="5" -->
 * Akka cluster
<!-- .element: class="fragment" data-fragment-index="5" -->
 * etc.
<!-- .element: class="fragment" data-fragment-index="5" -->

---

<!-- .slide: class="narrow" -->

### SGANARELLE
<!-- .element: class="fragment" data-fragment-index="1" -->

> Maître, mandez-moi n'importe quoi à faire. Je le ferai peut-être, à mon aise
<!-- .element: class="fragment" data-fragment-index="1" -->

### DON JUAN
<!-- .element: class="fragment" data-fragment-index="2" -->

> Réponds-moi donc ce que tu voudras, je ferai comme si c'était la réponse que j'attendais
<!-- .element: class="fragment" data-fragment-index="2" -->

### DONE ELVIRE
<!-- .element: class="fragment" data-fragment-index="3" -->

> ClassCastException(« oui, je vois bien que vous ne m'y attendiez pas »)
<!-- .element: class="fragment" data-fragment-index="3" -->

---

<!-- .slide: data-transition="slide-in fade-out" -->

## Akka Untyped
##### Maître, mandez-moi n'importe quoi à faire. Je le ferai peut-être, à mon aise
```scala
class HelloActor extends Actor {
  def receive = {
    case Pomme ⇒
      println("la pomme")

    case p: Poire ⇒
      println(s"une poire: $p")

    case s: List[Scoubidou] ⇒
      println(s"des scoubidous: ${s.map(_.couleur).mkString}")
  }
}
```

```scala
helloRef ! Pomme
"la pomme"
```
<!-- .element: class="fragment" data-fragment-index="1" -->


```scala
helloRef ! 42 
```
<!-- .element: class="fragment" data-fragment-index="2" -->

___

<!-- .slide: data-transition="fade-in fade-out" data-background="../img/rasmus-dead-letters.jpg" -->

## Akka Untyped
##### Maître, mandez-moi n'importe quoi à faire. Je le ferai peut-être, à mon aise


```scala
class HelloActor extends Actor {
  def receive = {
    case Pomme ⇒
      println("la pomme")

    case p: Poire ⇒
      println(s"une poire: $p")

    case s: List[Scoubidou] ⇒
      println(s"des scoubidous: ${s.map(_.couleur).mkString}")
  }
}
```

```scala
helloRef ! Pomme
"la pomme"
```

```scala
helloRef ! 42 // → DEAD LETTER
```

___

<!-- .slide: data-transition="fade-in fade-out" data-background="../img/rasmus-dead-letters.jpg" -->

## Akka Untyped
##### Maître, mandez-moi n'importe quoi à faire. Je le ferai peut-être, à mon aise

```scala
class HelloActor extends Actor {
  def receive = {
    case Pomme ⇒
      println("la pomme")

    case p: Poire ⇒
      println(s"une poire: $p")

    case s: List[Scoubidou] ⇒
      println(s"des scoubidous: ${s.map(_.couleur).mkString}")
  }
}
```

```scala
helloRef ! Pomme
"la pomme"
```

```scala
helloRef ! 42 // → DEAD LETTER
```

```scala
helloRef ! (1 :: 2 :: 3 :: Nil)
```

___

## Akka Untyped
##### Maître, mandez-moi n'importe quoi à faire. Je le ferai peut-être, à mon aise
<!-- .slide: data-transition="fade-in fade-out" data-background="../img/rammstein-benzin.jpg" -->

```scala
class HelloActor extends Actor {
  def receive = {
    case Pomme ⇒
      println("la pomme")

    case p: Poire ⇒
      println(s"une poire: $p")

    case s: List[Scoubidou] ⇒
      println(s"des scoubidous: ${s.map(_.couleur).mkString}")
  }
}
```
```scala
helloRef ! Pomme
"la pomme"
```

```scala
helloRef ! 42 // → DEAD LETTER
```

```scala
helloRef ! (1 :: 2 :: 3 :: Nil) // → ClassCastException
```

___

## Akka Untyped
##### Maître, mandez-moi n'importe quoi à faire. Je le ferai peut-être, à mon aise
<!-- .slide: data-transition="fade-in fade-out" data-background="../img/rammstein-benzin.jpg" -->

```scala
class HelloActor extends Actor {
  def receive = {
    case Pomme ⇒
      println("la pomme")

    case p: Poire ⇒
      println(s"une poire: $p")

    case s: List[Scoubidou] ⇒
    /* non-variable type argument Scoubidou 
    in type pattern List[Scoubidou] (the underlying 
    of List[Scoubidou]) is unchecked since 
    it is eliminated by erasure */
      println(s"des scoubidous: ${s.map(_.couleur).mkString}")
      
  }
}
```

___

<!-- .slide: data-transition="fade-in slide-out" -->

## Akka Untyped

```scala
class HelloActor extends Actor {
  def receive: PartialFunction[Any, Unit] = {
            /* ▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲ */

    case Pomme ⇒
      println("la pomme")

    case p: Poire ⇒
      println(s"une poire: $p")

    case s: List[Scoubidou] ⇒
      println(s"des scoubidous: ${s.map(_.couleur).mkString}")
  }
}
```

> Maître, mandez-moi n'importe quoi à faire. Je le ferai peut-être, à mon aise 
<!-- .element: class="fragment" data-fragment-index="1" -->

notes:
Hat-tip to Valentin Kasas pour la forme originale de la citation

___

<!-- .slide: data-transition="fade-in slide-out" -->

## Akka Untyped
##### Réponds-moi donc ce que tu voudras, je ferai comme si c'était la réponse que j'attendais
```scala
class RequestResponseActor extends Actor {
    def receive = {
      case Question ⇒
        sender() ! (1 :: 2 :: 3 :: Nil)
    }
}
```

```
val boxOfChocolates = (rrRef ? Question)
```

```scala
    // boxOfChocolates: Future[Any]
``` 
<!-- .element: class="fragment" data-fragment-index="1" -->

```
val resp2 = (rrRef ? Question).mapTo[List[Scoubidou]]
```
 <!-- .element: class="fragment" data-fragment-index="2" -->

```scala
    // resp2: Future[List[Scoubidou]]
```
<!-- .element: class="fragment" data-fragment-index="3" -->

```
val result = Await.result(resp2, timeout.duration)
``` 
<!-- .element: class="fragment" data-fragment-index="4" -->

```scala
    // ClassCastException
```
<!-- .element: class="fragment" data-fragment-index="5" -->


___

<!-- .slide: data-transition="fade-in slide-out" -->

## Akka Untyped

Akka, c'est très bien, mais&nbsp;:
* Messages de types « aléatoires »
<!-- .element: class="fragment" data-fragment-index="1" -->
* Aucune certitude sur le type de messages
<!-- .element: class="fragment" data-fragment-index="2" -->
* Aucune certitude sur le type des réponses
<!-- .element: class="fragment" data-fragment-index="3" -->
* &nbsp;
<!-- .element: class="fragment" data-fragment-index="4" -->
```scala
class SomeActor[T] extends Actor {
   def receive = {
      case m: GenericMsg[T] ⇒ … 
   }
}
```
<!-- .element: class="fragment" data-fragment-index="4" -->
* Mutable way of life
<!-- .element: class="fragment" data-fragment-index="5" -->
* Difficile à tester
<!-- .element: class="fragment" data-fragment-index="6" -->

notes:
"difficile à tester": la machinerie de transport de messages, asynchrone, rend le test 
en boîte blanche vraiment compliqué

---

## Akka Typed

---

## Akka Typed (2015/2017)

* Expérimentations avec Akka 2.4, apparu avec Akka 2.5
<!-- .element: class="fragment" data-fragment-index="1" -->
* Tout est typé
<!-- .element: class="fragment" data-fragment-index="2" -->
* La règle, c'est l'immutabilité
<!-- .element: class="fragment" data-fragment-index="3" -->
* Chaque acteur est une machine à états
<!-- .element: class="fragment" data-fragment-index="4" -->
* …mais ça reste interopérable avec Akka Untyped
<!-- .element: class="fragment" data-fragment-index="5" -->

---

## Akka Typed

Attention peinture fraîche :
* Intention de support équivalent à Akka « Untyped »
<!-- .element: class="fragment" data-fragment-index="1" -->
* Règle SemVer pas encore en vigueur
<!-- .element: class="fragment" data-fragment-index="2" -->
* 2.5.{ 14→15 } contient des changements breakants
<!-- .element: class="fragment" data-fragment-index="3" -->

---

## Mon premier acteur typé
<!-- .slide: data-transition="slide-in fade-out"  -->

```scala
object MyFirstTypedActor {
  def hello: Behavior[Int] =                            
              
                                           

                          
  
}
```

```scala
val myFirst = context.spawn(MyFirstTypedActor.hello, "hello")



myFirst ! 7
```
<!-- .element: class="always-hidden" -->

```text
received a number: 7
```
<!-- .element: class="always-hidden" -->

___

## Mon premier acteur typé
<!-- .slide: data-transition="fade-in fade-out"  -->

```scala
object MyFirstTypedActor {
  def hello: Behavior[Int] = Behaviors.receiveMessage {
    msg ⇒
      println(s"received a number: $msg")

      Behaviors.same
  }
}
```

```scala
val myFirst = context.spawn(MyFirstTypedActor.hello, "hello")



myFirst ! 7
```
<!-- .element: class="fragment" data-fragment-index="1" -->

```text
received a number: 7
```
<!-- .element: class="fragment" data-fragment-index="2" -->

___

## Mon premier acteur typé
<!-- .slide: data-transition="fade-in fade-out"  -->

```scala
object MyFirstTypedActor {
  def hello: Behavior[Int] = Behaviors.receiveMessage {
    msg ⇒                              // msg est un Int
      println(s"received a number: $msg")

      Behaviors.same
  }
}
```

```scala
val myFirst = context.spawn(MyFirstTypedActor.hello, "hello")
    // myFirst: ActorRef[Int]   


myFirst ! 7
```

```text
received a number: 7
```
<!-- .element: class="always-hidden" -->

___

## Mon premier acteur typé
<!-- .slide: data-transition="fade-in fade-out"  -->

```scala
object MyFirstTypedActor {
  def hello: Behavior[Int] = Behaviors.receiveMessage {
    msg ⇒                              // msg est un Int
      println(s"received a number: $msg")

      Behaviors.same
  }
}
```

```scala
val myFirst = context.spawn(MyFirstTypedActor.hello, "hello")
    // myFirst: ActorRef[Int]   


myFirst ! "\uD83D\uDE08"
```

```text
received a number: 7
```
<!-- .element: class="always-hidden" -->

___

## Mon premier acteur typé
<!-- .slide: data-transition="fade-in slide-out"  -->

```scala
object MyFirstTypedActor {
  def hello: Behavior[Int] = Behaviors.receiveMessage {
    msg ⇒                              // msg est un Int
      println(s"received a number: $msg")

      Behaviors.same
  }
}
```

```scala
val myFirst = context.spawn(MyFirstTypedActor.hello, "hello")
    // myFirst: ActorRef[Int]   


myFirst ! "\uD83D\uDE08" // DOES NOT COMPILE !!
    // type mismatch: found String("😈") required: Int
```

```text
received a number: 7
```
<!-- .element: class="always-hidden" -->

---

<!-- .slide: data-background="../img/elon-yes.jpg" -->
## Enfin !

---

## Renvoyer des réponses
<!-- .slide: data-transition="slide-in slide-out"  -->

```scala
object MyFirstTypedActor {
  def hello: Behavior[Int] = Behaviors.receiveMessage {
    msg ⇒                              // msg est un Int
      ??? // sender() ! msg + 1

      Behaviors.same
  }
}
```

```scala
val response = myFirst ? 7
```

___

## Renvoyer des réponses
<!-- .slide: data-transition="slide-in slide-out"  -->

Comment répondre ?

<span>En Akka Untyped, on a `sender()`&nbsp;:</span>
<!-- .element: class="fragment" data-fragment-index="1" -->
```
def sender(): ActorRef
```
<!-- .element: class="fragment" data-fragment-index="1" -->

<span>En Akka Typed, il faut prévoir&nbsp;:</span>
<!-- .element: class="fragment" data-fragment-index="2" -->
```
case class Question(… , replyTo: ActorRef[ExpectedReply])
```
<!-- .element: class="fragment" data-fragment-index="3" -->

note: en plus ça résout le problème de la capture de sender()

___

## Renvoyer des réponses
<!-- .slide: data-transition="slide-in fade-out" -->
La méthode officielle selon la doc :
```scala
object MyFirstRequestReplyActor {
  case class Query(value: Int, replyTo: ActorRef[Response])
  case class Response(value: Int)







}
```

```scala
val fResponse: Future[MyFirstRequestReplyActor.Response] =
  rref ? (ref ⇒ MyFirstRequestReplyActor.Query(value, ref))

fResponse.map { response => response.value shouldEqual (value + 1) }
```
<!-- .element: class="always-hidden" -->

___

## Renvoyer des réponses
<!-- .slide: data-transition="fade-in fade-out" -->
La méthode officielle selon la doc :
```scala
object MyFirstRequestReplyActor {
  case class Query(value: Int, replyTo: ActorRef[Response])
  case class Response(value: Int)

  def hello: Behavior[Query] = Behaviors.receiveMessage {
    msg ⇒
      msg.replyTo ! Response(msg.value + 1)

      Behaviors.same
  }
}
```

```scala
val fResponse: Future[MyFirstRequestReplyActor.Response] =
  rref ? (ref ⇒ MyFirstRequestReplyActor.Query(value, ref))

fResponse.map { response => response.value shouldEqual (value + 1) }
```
<!-- .element: class="fragment" data-fragment-index="1" -->

___

## Renvoyer des réponses
<!-- .slide: data-transition="fade-in fade-out" -->
Ça marche aussi
```scala
object MyFirstRequestReplyActor {
  case class Query(value: Int, replyTo: ActorRef[Response])
  case class Response(value: Int)

  def hello: Behavior[Query] = Behaviors.receiveMessage {
    msg ⇒
      msg.replyTo ! Response(msg.value + 1)

      Behaviors.same
  }
}
```

```scala
val fResponse = rref ? MyFirstRequestReplyActor.Query(value,
      (_:ActorRef[MyFirstRequestReplyActor.Response]))

fResponse.map { response => response.value shouldEqual (value + 1) }
```

___

## Renvoyer des réponses
<!-- .slide: data-transition="fade-in fade-out" -->
Astuce :
```scala
object MyFirstRequestReplyActor {
  case class Query(value: Int)(val replyTo: ActorRef[Response]) 
  case class Response(value: Int)
                             //
  def hello: Behavior[Query] = Behaviors.receiveMessage {
    msg ⇒
      msg.replyTo ! Response(msg.value + 1)

      Behaviors.same
  }
}
```

```scala
    val fResponse = ref ? MyFirstRequestReplyActor.Query(value)


    fResponse.map { response => response.value shouldEqual (value + 1) }
```
<!-- .element: class="fragment" data-fragment-index="1" -->

___

## Renvoyer des réponses
<!-- .slide: data-transition="fade-in slide-out" -->
Astuce :
```scala
object MyFirstRequestReplyActor {
  case class Query(value: Int)(val replyTo: ActorRef[Response]) 
  case class Response(value: Int)
                             //
  def hello: Behavior[Query] = Behaviors.receiveMessage {
    msg ⇒
      msg.replyTo ! Response(msg.value + 1)

      Behaviors.same
  }
}
```

```scala
    val fResponse = ref ? MyFirstRequestReplyActor.Query(value)
        // fResponse: Future[MyFirstRequestReplyActor.Response]

    fResponse.map { response => response.value shouldEqual (value + 1) }
```

---

## Accepter plusieurs messages
<!-- .slide: data-transition="slide-in fade-out" -->
Accepter plusieurs types de messages&nbsp;?

```scala
case object Pomme
case class Poire(variety: String)
case class Scoubidous(content: List[Scoubidou])
```

```scala
def myBehavior: Behavior[ _ ] = Behaviors.receive {
                      
                         
                                  
                              
}
```
<!-- .element: class="fragment" data-fragment-index="1" -->

___

## Accepter plusieurs messages
<!-- .slide: data-transition="fade-in fade-out" -->
Accepter plusieurs types de messages&nbsp;?

```scala
case object Pomme
case class Poire(variety: String)
case class Scoubidous(content: List[Scoubidou])
```

```scala
def myBehavior: Behavior[ _ ] = Behaviors.receive {
   case Pomme ⇒ ???
   case p: Poire ⇒ ???
   case Scoubidous(lh :: lt) ⇒ ???
   case Scoubidous(Nil) ⇒ ???
}
```

___

## Accepter plusieurs messages
<!-- .slide: data-transition="fade-in fade-out" -->
Oui, s'ils ont un supertype commun :

```scala
trait Message
case object Pomme extends Message
case class Poire(variety: String) extends Message
case class Scoubidous(content: List[Scoubidou]) extends Message
```

```scala
def myBehavior: Behavior[Message] = Behaviors.receive {
   case Pomme ⇒ ???
   case p: Poire ⇒ ???
   case Scoubidous(lh :: lt) ⇒ ???
   case Scoubidous(Nil) ⇒ ???
}
```

notes:
PAS ENCORE CONTRÔLÉ PAR LE COMPILATEUR

___

## Accepter plusieurs messages
<!-- .slide: data-transition="fade-in slide-out" -->
Tant qu'à faire on peut en faire un ADT

```scala
sealed trait Message
case object Pomme extends Message
final case class Poire(variety: String) extends Message
final case class Scoubidous(content: List[Scoubidou]) extends Message
```

```scala
def myBehavior: Behavior[Message] = Behaviors.receive {
   case Pomme ⇒ ???
   case p: Poire ⇒ ???
   case Scoubidous(lh :: lt) ⇒ ???
   case Scoubidous(Nil) ⇒ ???
}
```

notes:
S'il manque des types dans mon receive, le compilateur va prévenir !

---

## Un acteur avec état

On va fabriquer un acteur qui&nbsp;:
<!-- .element: class="fragment" data-fragment-index="1" -->
* embarque un compteur qu'on peut incrémenter ou décrémenter
<!-- .element: class="fragment" data-fragment-index="2" -->
* garde la trace du « mini » et du « maxi » historique
<!-- .element: class="fragment" data-fragment-index="3" -->

Cet acteur va donc recevoir&nbsp;:
<!-- .element: class="fragment" data-fragment-index="4" -->
* des commandes (Up / Down)
<!-- .element:  class="fragment" data-fragment-index="5" -->
* et des requêtes (GetCurrentState)
<!-- .element:  class="fragment" data-fragment-index="6" -->

___

## Un acteur avec état
<!-- .slide: data-transition="slide-in fade-out" class="warning-verbose" -->

```scala
object CounterService {
  /* Protocol */
 
  /* Internals */
  
  /* Public */
    
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {

  /* Protocol */
  sealed trait Message
  object Message {


      final case class Up()(val replyTo: ActorRef[Boolean]) extends Message
      final case class Down()(val replyTo: ActorRef[Boolean]) extends Message

  }

  /* Internals */
  /* Public */  
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {
  /* Protocol */
  sealed trait Message ✂
  
  /* Internals */

   

  /* Public */  

}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {
  /* Protocol */
  sealed trait Message ✂
  
  /* Internals */
  private final case class InternalState(current: Int, lowest: Int, highest: Int, 
                                         min: Int, max: Int)  


  /* Public */  

}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {

  /* Protocol */
  sealed trait Message ✂

  /* Internals */
  private final case class InternalState(current: Int, lowest: Int, highest: Int, 
                                         min: Int, max: Int) {
    def up(): InternalState = if (current < max) {
      val ncurrent = current + 1
      this.copy(current = ncurrent, highest = Math.max(highest, ncurrent))
    } else this

    def down(): InternalState = /* ✂ idem mutatis mutandis */
  }

  /* Public */
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {
  /* Protocol */
  sealed trait Message ✂

  /* Internals */
  final case class InternalState( … ) { ✂ }



   
   
  /* Public */  
  
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {

  /* Protocol */
  sealed trait Message ✂

  /* Internals */
  final case class InternalState( … ) { ✂ }

  private def running(state: InternalState): Behavior[Message] = ???                       


  /* Public */  
  
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {

  /* Protocol */
  sealed trait Message ✂

  /* Internals */
  final case class InternalState( … ) { ✂ }

  private def running(state: InternalState): Behavior[Message] = Behaviors.receiveMessage {
    case msg: Message.Up ⇒
      val nstate = state.up()
      msg.replyTo ! (nstate ne state)
      running(nstate)

  }
  /* Public */
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {

  /* Protocol */
  sealed trait Message ✂

  /* Internals */
  final case class InternalState( … ) { ✂ }

  private def running(state: InternalState): Behavior[Message] = Behaviors.receiveMessage {
    case msg: Message.Up ⇒
      val nstate = state.up()
      msg.replyTo ! (nstate ne state)
      running(nstate)
      /* ⚠ WARNING: match may not be exhaustive. It would fail on the following input: Message.Down()  */
  }
  /* Public */
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {
  /* Protocol */

  sealed trait Message ✂

  /* Internals */
  final case class InternalState( … ) { ✂ }

  private def running(state: InternalState): Behavior[Message] = Behaviors.receiveMessage {
    case msg: Message.Up ⇒
      val nstate = state.up()
      msg.replyTo ! (nstate ne state)
      running(nstate)

    case msg: Message.Down ⇒ // same
  }
  /* Public */
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {

  /* Protocol */
  sealed trait Message
  object Message {

      final case class Up()(val replyTo: ActorRef[Boolean])                
      final case class Down()(val replyTo: ActorRef[Boolean])                 

  }


  /* Internals */
  /* Public */ 
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {
  /* Protocol */

  sealed trait Message

  object Message {
      sealed trait Command extends Message
      sealed trait Query extends Message

      final case class Up()(val replyTo: ActorRef[Boolean]) extends Command
      final case class Down()(val replyTo: ActorRef[Boolean]) extends Command

  }

  /* Internals */
  /* Public */ 
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {
  /* Protocol */

  sealed trait Message
  object Message {
      sealed trait Command extends Message
      sealed trait Query extends Message

      final case class Up()(val replyTo: ActorRef[Boolean]) extends Command
      final case class Down()(val replyTo: ActorRef[Boolean]) extends Command

      final case class GetCurrentState()(val replyTo: ActorRef[CurrentState]) extends Query
  }

  final case class CurrentState(current: Int, lowest: Int, highest: Int)

  /* Internals */
  /* Public */ 
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {
  /* Protocol */
  /* Internals */

  final case class InternalState( … ) { ✂ }

  private def running(state: InternalState): Behavior[Message] = Behaviors.receiveMessage {

    case msg: Message.Up ⇒
      val nstate = state.up()
      msg.replyTo ! (nstate ne state)
      running(nstate)

    case msg: Message.Down ⇒ /* ✂ */
  }
  /* Public */  
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object CounterService {

  /* Protocol */
  /* Internals */
  private def running(state: InternalState): Behavior[Message] = Behaviors.receiveMessage {

    case msg: Message.GetCurrentState ⇒
      msg.replyTo ! Message.CurrentState(state.current, state.lowest, state.highest)
      Behaviors.same

    case msg: Message.Up ⇒
      val nstate = state.up()
      msg.replyTo ! (nstate ne state)
      running(nstate)

    case msg: Message.Down ⇒ /* ✂ */
  }
  /* Public */
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in slide-out" class="warning-verbose" -->

```scala
object CounterService {
  /* Protocol */
  /* Internals */
  private def running(state: InternalState): Behavior[Message] = Behaviors.receiveMessage {
    case query: Message.Query ⇒ query match {
         case msg ⇒ Message.GetCurrentState ⇒
            msg.replyTo ! Message.CurrentState(state.current, state.lowest, state.highest)
      }
      Behaviors.same

    case command: Message.Commmand ⇒ command match {
        case msg: Message.Up ⇒ 
          val nstate = state.up()
          msg.replyTo ! (nstate ne state)
          running(nstate)

        case msg: Message.Down ⇒ /* ✂ */
      }
    }
  /* Public */
}
```

___

## Un acteur avec état
<!-- .slide: data-transition="slide-in fade-out" class="warning-verbose" -->
```scala
object CounterService {
  /* Protocol */
  /* Internals */
  
  final case class InternalState( … ) { ✂ }
  def running(state:InternalState): Behavior[Message] = ✂
  
  /* Public */


}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->
```scala
object CounterService {
  /* Protocol */
  /* Internals */
  
  final case class InternalState( … ) { ✂ }
  def running(state:InternalState): Behavior[Message] = ✂
  
  /* Public */
  
  def start(min: Int, max: Int): Behavior[Message] = ???
                                      
                                                                              
   

}
```

___

## Un acteur avec état
<!-- .slide: data-transition="fade-in slide-out" class="warning-verbose" -->
```scala
object CounterService {
  /* Protocol */
  /* Internals */
  
  final case class InternalState( … ) { ✂ }
  def running(state:InternalState): Behavior[Message] = ✂
  
  /* Public */
  
  def start(min: Int, max: Int): Behavior[Message] = Behaviors.setup { context ⇒
    val initialValue = (min + max)/2
    running(InternalState(initialValue, initialValue, initialValue, min, max))
  }

}
```

___

## Un acteur avec état

On essaye ?

___

## essayons
<!-- .slide: data-transition="slide-in fade-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {




    val system = ActorSystem(   ???  , "MySystem")
  
   /* … */
 }       
}


```

___

## essayons
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    final case class GetCounter()(val replyTo: ActorRef[ActorRef[CounterService.Message]])

    def guardian: Behavior[GetCounter] = ???                

             
     

    val system = ActorSystem(guardian, "MySystem")
  
   /* … */
 }       
}
```

___

## essayons
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    final case class GetCounter()(val replyTo: ActorRef[ActorRef[CounterService.Message]])

    def guardian: Behavior[GetCounter] = Behaviors.setup {
      context ⇒
        val counter: ActorRef[CounterService.Message] = ???

        Behaviors.receiveMessage {
          msg ⇒
            msg.replyTo ! counter
            Behaviors.same
        }
    }

    val system = ActorSystem(guardian, "MySystem")
  
   /* … */
 }       
}
```

___

## essayons
<!-- .slide: data-transition="fade-in slide-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    final case class GetCounter()(val replyTo: ActorRef[ActorRef[CounterService.Message]])

    def guardian: Behavior[GetCounter] = Behaviors.setup {
      context ⇒
        val counter = context.spawn(CounterService.start(-100, 100), "stateful")

        Behaviors.receiveMessage {
          msg ⇒
            msg.replyTo ! counter
            Behaviors.same
        }
    }

    val system = ActorSystem(guardian, "MySystem")
  
   /* … */
 }       
}
```

___

## essayons
<!-- .slide: data-transition="slide-in fade-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    /* ✂ */
    
    val system = ActorSystem(guardian, "MySystem")

    implicit def executor: ExecutionContext = system.toUntyped.dispatcher
    implicit def scheduler: Scheduler = system.scheduler
    implicit val timeout: Timeout = Timeout(300, TimeUnit.SECONDS)



    /* … */
  }
}
```

___

## essayons
<!-- .slide: data-transition="fade-in slide-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    /* ✂ */
    
    val system = ActorSystem(guardian, "MySystem")

    implicit def executor: ExecutionContext = system.toUntyped.dispatcher
    implicit def scheduler: Scheduler = system.scheduler
    implicit val timeout: Timeout = Timeout(300, TimeUnit.SECONDS)

    val fCounter: Future[ActorRef[CounterService.Message]] = system ? GetCounter()

    /* … */
  }
}
```

___

## essayons
<!-- .slide: data-transition="slide-in fade-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    /* ✂ */
    
    val fCounter = system ? GetCounter()
    

             

    /* … */
  }
}
```

___

## essayons
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    /* ✂ */
    
    val fCounter = system ? GetCounter()
    
    val movements = Stream.iterate(0)(_ ⇒ Random.nextInt(3) - 1).filterNot(_ == 0).take(1000)

             

    /* … */
  }
}
```

___

## essayons
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    /* ✂ */
    
    val fCounter = system ? GetCounter()
    
    val movements = Stream.iterate(0)(_ ⇒ Random.nextInt(3) - 1).filterNot(_ == 0).take(1000)
        // movements = Stream(1,-1,1,1,1,-1,-1,1,-1,1, …)

                     
               

    /* … */
  }
}
```

___

## essayons
<!-- .slide: data-transition="fade-in slide-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    /* ✂ */
    val fCounter = system ? GetCounter()
    
    val movements = Stream.iterate(0)(_ ⇒ Random.nextInt(3) - 1).filterNot(_ == 0).take(1000)
        // movements = Stream(1,-1,1,1,1,-1,-1,1,-1,1, …)
        
    val fMotionDone = movements.foldLeft(fCounter) { case (before, movement) ⇒
      for {
        counter ← before
        _ ← movement match {
          case -1 ⇒ counter ? Down()
          case 1 ⇒ counter ? Up()
          case _ ⇒ Future.successful(false)
        }
      } yield counter
    }
    /* … */
  }
}
```

___

## essayons
<!-- .slide: data-transition="slide-in fade-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    /* ✂ */
    
    val fMotionDone: Future[ActorRef[CounterService.Message]] = /* ✀ */

                                    

                                                      
  }
}
```

```text
lo=-85, hi=1
```
<!-- .element: class="always-hidden" -->

___

## essayons
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    /* ✂ */
    
    val fMotionDone: Future[ActorRef[CounterService.Message]] = /* ✀ */

    val fFinalState = for {
      counter ← fMotionDone
      current ← counter ? GetCurrentState()
    } yield {
      (current.lowest, current.highest)
    }


                                                      
  }
}
```

```text
lo=-85, hi=1
```
<!-- .element: class="always-hidden" -->

___

## essayons
<!-- .slide: data-transition="fade-in fade-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    /* ✂ */
    
    val fMotionDone: Future[ActorRef[CounterService.Message]] = /* ✀ */

    val fFinalState = for {
      counter ← fMotionDone
      current ← counter ? GetCurrentState()
    } yield {
      (current.lowest, current.highest)
    }

    val (lo, hi) = Await.result(fFinalState, timeout.duration)
    println(s"lo=${lo}, hi=${hi}")

                                                        
  }
}
```

```text
lo=-85, hi=1
```
<!-- .element: class="always-hidden" -->

___

## essayons
<!-- .slide: data-transition="fade-in slide-out" class="warning-verbose" -->

```scala
object TryStatefulActor {
  def main(args: Array[String]): Unit = {
    /* ✂ */
    
    val fMotionDone: Future[ActorRef[CounterService.Message]] = /* ✀ */

    val fFinalState = for {
      counter ← fMotionDone
      current ← counter ? GetCurrentState()
    } yield {
      (current.lowest, current.highest)
    }

    val (lo, hi) = Await.result(fFinalState, timeout.duration)
    println(s"lo=${lo}, hi=${hi}")

    Await.result(system.terminate(), timeout.duration)
  }
}
```

```text
lo=-85, hi=1
```
<!-- .element: class="fragment" data-fragment-index="1" -->

---

<!-- .slide: data-transition="slide-in fade-out" data-background="../img/maci-clean.jpg"-->

___

<!-- .slide: data-transition="fade-in slide-out" data-background="../img/maci-clean-assombri.jpg"-->

## Ne pas confondre

```scala 
Behavior[MessageT]
```
<!-- .element: class="fragment" data-fragment-index="1" -->

trait dont héritent les comportements
<!-- .element: class="fragment" data-fragment-index="3" -->

et
<!-- .element: class="always-hidden" --> 

```scala 
Behaviors
```
<!-- .element: class="fragment" data-fragment-index="2" -->

Objet-usine qui porte des méthodes qui fabriquent ces comportements
<!-- .element: class="fragment" data-fragment-index="4" -->

---

## Actor Selection n'est plus

Soient deux acteurs `Poule` et `Œuf`, qui doivent connaître mutuellement leur référence pour s'échanger 
des messages.

Comment faire ?
<!-- .element: class="fragment" data-fragment-index="1" -->

___

## Actor Selection n'est plus

Poule ←→ Œuf

Comment faire ? (1)

* `Œuf` fait un `self.actorSelection("../Poule")` et obtient l'`ActorRef` de Poule 

ça fonctionnait en Akka Untyped
<!-- .element: class="fragment" data-fragment-index="1" -->

___

## Actor Selection n'est plus

Comment faire ? (1)

en Akka Untyped, actorSelection permettait des choses bizarres :
```scala
system.actorSelection("/user/*") ! "Salut la compagnie !"
```

```scala
    // quel type ? Pourquoi ? 
```
<!-- .element: class="fragment" data-fragment-index="1" -->

<span>`actorSelection` ne **peut pas** être typé proprement. Il a donc dû disparaître.</span>
<!-- .element: class="fragment" data-fragment-index="2" -->

___

## Actor Lookup n'est plus

Poule ←→ Œuf

Comment faire ? (2)

<div>
* le gardien G est responsable :
 * le gardien G crée Poule, 
 * puis le gardien crée Œuf (lui donne `ActorRef[Poule]`)
 * puis le gardien (ou Œuf) envoie un message à Poule pour lui donner la référence de Œuf  
</div>
<!-- .element: class="fragment" data-fragment-index="1" -->

ça fonctionne, en Typed comme en Untyped, mais c'est compliqué
<!-- .element: class="fragment" data-fragment-index="2" -->

___

<!-- .slide: data-transition="slide-in slide-out" data-background="../img/gbh-receptionist-assombri.jpg"-->
## <span style="text-decoration: line-through">Actor Lookup</span> → <span>Receptionist</span>

```scala
val CounterServiceKey = ServiceKey[CounterService.Message]("counterService")
``` 
<!-- .element: class="fragment" data-fragment-index="1" -->

```scala
def start: Behavior[CounterService.Message] = Behaviors.setup { context ⇒ 
  context.system.receptionist ! Receptionist.Register(CounterServiceKey, context.self)
  
  Behaviors.receive { ??? }
} 
```
<!-- .element: class="fragment" data-fragment-index="2" -->

```scala
def receiveCounters: Behavior[Receptionist.Listing] = Behaviors.receiveMessage {
  case Receptionist.Listing(listings) ⇒
    // do something with all these new ActorRef[CounterService.Message] 
    Behaviors.same
}
```
<!-- .element: class="fragment" data-fragment-index="3" -->
```scala
val receiverRef: ActorRef[Receptionist.Listing] = context.spawnAnonymous(receiveCounters)

context.system.receptionist ! Receptionist.Subscribe(CounterServiceKey, receiver)
```
<!-- .element: class="fragment" data-fragment-index="4" -->

Bonus : ça fonctione « out of the box » en cluster.
<!-- .element: class="fragment" data-fragment-index="5" -->

notes: y'a même un singleton sur le même principe; cluster-wide at-most-one instance.

---

## FSM as a First-Class Citizen
<!-- .slide: data-transition="fade-in fade-out" -->

___

## FSM as a First-Class Citizen
<!-- .slide: data-transition="slide-in fade-out" -->

Pourquoi tous ces `Behaviors.same` dans la définition de comportement ?
```scala
final case class GetCounter()(val replyTo: ActorRef[ActorRef[CounterService.Message]])

def guardian: Behavior[GetCounter] = Behaviors.setup {
  context ⇒
    val counter = context.spawn(CounterService.start(-100, 100), "stateful")

    Behaviors.receiveMessage {
      msg ⇒
        msg.replyTo ! counter
        Behaviors.same
    }
}
```
<!-- .element: class="fragment" data-fragment-index="1" -->

___

## FSM as a First-Class Citizen
<!-- .slide: data-transition="fade-in fade-out" -->

Pourquoi tous ces `Behaviors.same` dans la définition de comportement ?
```scala
final case class GetCounter()(val replyTo: ActorRef[ActorRef[CounterService.Message]])

def lazyGuardian: Behavior[GetCounter] = Behaviors.setup {
  context ⇒ lazyGuardianNotYetRunning(context)
}
```

```scala
def lazyGuardianNotYetRunning(context: ActorContext[GetCounter]) = 
    Behaviors.receiveMessage {
      msg =>
        val counterRef = context.spawn(CounterService.start(-100, 100), "stateful")
        msg.replyTo ! counterRef
        lazyGuardianRunning(counterRef)
    }
```
<!-- .element: class="fragment" data-fragment-index="1" -->

```scala
def lazyGuardianRunning(counterRef: ActorRef[CounterService.Message]): Behavior[GetCounter] =
    Behaviors.receiveMessage {
      msg ⇒
        msg.replyTo ! counterRef
        Behaviors.same
    }

```
<!-- .element: class="fragment" data-fragment-index="2" -->

---

## Timers
<!-- .slide: data-transition="slide-in fade-out" -->
On peut accéder à l'horloge système pour se faire envoyer des messages :

```scala

val myBehavior: Behavior[Message] = Behaviors.setup { context ⇒
  /* code d'initialisation ici */
                                          
    
                         
                              
                                                                        
                        
      
                               
         
 
}

```

___

## Timers
<!-- .slide: data-transition="fade-in fade-out" -->
On peut accéder à l'horloge système pour se faire envoyer des messages :

```scala

val myBehavior: Behavior[Message] = Behaviors.setup { context ⇒
  /* code d'initialisation ici */
  Behaviors.withTimers { timerFactory ⇒
    
                          
                              
                                                                         
                          
      
                                 
      
  } 
}

```

___

## Timers
<!-- .slide: data-transition="fade-in fade-out" -->
On peut accéder à l'horloge système pour se faire envoyer des messages :

```scala

val myBehavior: Behavior[Message] = Behaviors.setup { context ⇒
  /* code d'initialisation ici */
  Behaviors.withTimers { timerFactory ⇒
    
    Behaviors.receive {
                            
                                                                         
                        
      
                               
    }
  } 
}

```

___

## Timers
<!-- .slide: data-transition="fade-in slide-out" -->
On peut accéder à l'horloge système pour se faire envoyer des messages :

```scala

val myBehavior: Behavior[Message] = Behaviors.setup { context ⇒
  /* code d'initialisation ici */
  Behaviors.withTimers { timerFactory ⇒
    
    Behaviors.receive {
      case Message.Start ⇒ 
        timerFactory.startSingleTimer( "key", Message.Tick, 1 second )
        Behaviors.same
      
      case Message.Tick ⇒ ???  
    }
  } 
}

```
notes:
FIXME: fade-in du code (par niveau d'emboîtement)

---

## Supervision
<!-- .slide: data-transition="slide-in slide-out" -->

```scala
val raw: Behavior[Message] = SomeService.start(…)
```

```scala
val reliable: Behavior[Message] = Behaviors.supervise(raw) 
    .onFailure[IllegalStateException](SupervisorStrategy.restartWithLimit(10, 1 minute)
          .withLoggingEnabled(true)
      )
    .onFailure[IllegalArgumentException](SupervisorStrategy.stop)
```

---

## Interop avec Akka Untyped
<!-- .slide: data-transition="slide-in slide-out" -->

```scala
import akka.actor.typed.{ActorRef, ActorSystem, Behavior}
import akka.actor.{ ActorRef ⇒ UntypedActorRef, ActorSystem ⇒ UntypedActorSystem }
import akka.actor.typed.scaladsl.adapter._
```
<!-- .element: class="fragment" data-fragment-index="1" -->

```scala
val system: ActorSystem[MessageType]
val untypedSystem = system.toUntyped 
val systemAgain = untype.toTyped[MessageType]
```
<!-- .element: class="fragment" data-fragment-index="2" -->

```scala

val typedRef = untypedSystem.spawn(Behaviors.ignore[MessageType], "hopla")
val untypedRef = typedRef.toUntyped
val typedAgain = untypedRef.toTyped[MessageType]
```
<!-- .element: class="fragment" data-fragment-index="3" -->

---

## Interop avec Akka Streams
<!-- .slide: data-transition="slide-in fade-out" -->

<span>On peut adapter un acteur en `source` Akka Streams :</span>
<!-- .element: class="fragment" data-fragment-index="1" -->

<img data-src="../img/akka-stream-interop-source-01.svg" width="1433" height="325"/>
<!-- .element: class="fragment" data-fragment-index="2" -->

___

## Interop avec Akka Streams
<!-- .slide: data-transition="fade-in fade-out" -->

On peut adapter un acteur en `source` Akka Streams :

<img data-src="../img/akka-stream-interop-source-02.svg" width="1433" height="325"/>

___

## Interop avec Akka Streams (2)
<!-- .slide: data-transition="fade-in fade-out" -->

On peut adapter un `vidage` Akka Streams à destination d'un acteur

<img data-src="../img/akka-stream-interop-sink-01.svg" width="1333" height="422"/>
<!-- .element: class="fragment" data-fragment-index="1" -->

___

## Interop avec Akka Streams (2)
<!-- .slide: data-transition="fade-in slide-out" -->

On peut adapter un `vidage` Akka Streams à destination d'un acteur

<img data-src="../img/akka-stream-interop-sink-02.svg" width="1333" height="422"/>

---

## Persistance
<!-- .slide: data-transition="slide-in slide-out" -->

```text
Acteur = 
   Mutation( (état_i, commande) ⇒ état_{i+1} )
   + État_0
```
<!-- .element: class="fragment" data-fragment-index="1" -->

```text
Acteur persistant = 
   Conversion(message ⇒ effet)
   + Mutation( (état_i, effet) ⇒ état_{i+1} )
   + État_0
```
<!-- .element: class="fragment" data-fragment-index="2" -->

On définit :
<!-- .element: class="fragment" data-fragment-index="3" -->
* un état initial (« vide »)
<!-- .element: class="fragment" data-fragment-index="4" -->
* des événements persistés en réponse aux commandes
<!-- .element: class="fragment" data-fragment-index="5" -->
* le moyen de jouer ces événements en mutations
<!-- .element: class="fragment" data-fragment-index="6" -->

___

## Persistance (quickie)
<!-- .slide: data-transition="slide-in fade-out" -->

```scala
object SomePersistentActor {

   sealed trait Command
   class State
   sealed trait Event

   def entity(id: String): Behavior[Command] = 
      PersistentBehaviors.receive[Command, Event, State](
        persistenceId = id,
        emptyState = State.Zero,
        commandHandler = ???, 
        eventHandler = ???)
}
```

___

## Persistance (quickie)
<!-- .slide: data-transition="fade-in slide-out" -->

```scala
object PersistentCounter {
   sealed trait Message
   sealed trait Command extends Message // up, down, ...
   
   sealed case class InternalState(/* ✂ */) /* serializable */
   
   /* NEW → */ sealed trait Event /* serializable */

   def entity(id: String): Behavior[Message] = 
      PersistentBehaviors.receive[Message, Event, InternalState](
        persistenceId = id,
        InternalState(50, 50, 50, -100, 100),
        commandHandler = ???, 
        eventHandler = ???)
}
```

___

## Persistance (quickie)
<!-- .slide: data-transition="fade-in fade-out" -->

```scala
   sealed trait Event
   final case class ChangeUp() extends Event
   final case class ChangeDown() extends Event

   private def commandHandler(state: InternalState, command: Command): 
    Effect[Event, InternalState] = command match {
                              
                                   

            
                                         
    }
```

___

## Persistance (quickie)
<!-- .slide: data-transition="fade-in fade-out" -->

```scala
   sealed trait Event
   final case class ChangeUp() extends Event
   final case class ChangeDown() extends Event

   private def commandHandler(state: InternalState, command: Command): 
    Effect[Event, InternalState] = command match {
      case Message.Up() => 
        Effect.persist(ChangeUp())

            
      case Message.Down() => ??? /* ✂ */
    }
```

___

## Persistance (quickie)
<!-- .slide: data-transition="fade-in slide-out" -->

```scala
   sealed trait Event
   final case class ChangeUp() extends Event
   final case class ChangeDown() extends Event

   private def commandHandler(state: InternalState, command: Command): 
    Effect[Event, InternalState] = command match {
      case Message.Up() => 
        Effect.persist(ChangeUp())
            .thenRun(_ => command.replyTo ! (state.up() ne state) )
            
      case Message.Down() => ??? /* ✂ */
    }
```

___

## Persistance (quickie)
<!-- .slide: data-transition="fade-in slide-out" -->

```scala
   private def eventHandler(state: InternalState, event: Event): InternalState = 
     event match {
      case ChangeUp() => state.up()
      case ChangeDown() => state.down()
     }
```

___

## Persistance

Pour une autre fois !

notes: Projection de vues, d'un agrégat à l'autre, etc.

---

## Tester ? BTK, ATK

En suivant la structure « État ; Comportement ; Acteur », on peut tester :
<!-- .element: class="fragment" data-fragment-index="1" -->

* l'état interne : un objet comme un autre
<!-- .element: class="fragment" data-fragment-index="2" -->
* le comportement : BehaviorTestKit
<!-- .element: class="fragment" data-fragment-index="3" -->
* l'acteur complet dans un contexte de laboratoire : ActorTestKit
<!-- .element: class="fragment" data-fragment-index="4" -->

notes:
sur l'objet interne: immuable, bien sûr!

___

## Tester : `BehaviorTestKit`

```scala
case class GetCounter()(val replyTo: ActorRef[ActorRef[CounterActor.Message]])

def lazyGuardian: Behavior[GetCounter] = /* ✂ */
```
<!-- .element: class="fragment" data-fragment-index="1" -->

```scala
  val testKit = BehaviorTestKit(lazyGuardian)
  val inbox = TestInbox[ActorRef[CounterActor.Message]]()
```
<!-- .element: class="fragment" data-fragment-index="2" -->

```scala  
  testKit.run(GetCounter()(inbox.ref))
```
<!-- .element: class="fragment" data-fragment-index="3" -->

```scala  
  val counterRef = inbox.receiveMessage()
  inbox.hasMessages shouldBe false
  testKit.expectEffect(Spawned(CounterActor.start(-100, 100), "counter"))
```
<!-- .element: class="fragment" data-fragment-index="4" -->

```scala  
  testKit.run(GetCounter()(inbox.ref))
```
<!-- .element: class="fragment" data-fragment-index="5" -->

```scala
  val counterRef2 = inbox.receiveMessage()
  inbox.hasMessages shouldBe false
  
  testKit.hasEffects() shouldBe false
  counterRef2 shouldEqual counterRef
```
<!-- .element: class="fragment" data-fragment-index="6" -->

___

## Tester : Actor Test Kit

```scala
class MySpec extends AsyncFlatSpec with Matchers with BeforeAndAfterAll {
  val testKit = ActorTestKit()
  override def afterAll(): Unit = testKit.shutdownTestKit()

  import testKit.{timeout, scheduler}
  implicit def executor: ExecutionContext = testKit.system.toUntyped.dispatcher
   
  val ref = system.spawn(MyActor.start, "MyActor")
  for {
     reponse <- Question(...)
  } yield {
     reponse.value shouldEqual 42
  }
}
```
<!-- .element: class="fragment" data-fragment-index="1" -->

---

## Akka Typed : conclusion

* Les messages sont fortement typés
<!-- .element: class="fragment" data-fragment-index="1" -->
* Les réponses sont fortement typées
<!-- .element: class="fragment" data-fragment-index="2" -->
* Acteur = (état + comportement) immuables + transitions nettes
<!-- .element: class="fragment" data-fragment-index="3" -->
* Plus facile à tester
<!-- .element: class="fragment" data-fragment-index="4" -->
* Oui, on peut échanger avec le monde legacy
<!-- .element: class="fragment" data-fragment-index="5" -->
* C'est pas tout à fait fini mais ça fonctionne très bien
<!-- .element: class="fragment" data-fragment-index="6" -->

---

<!-- .slide: data-background="../img/thats-all-folks.jpg" -->
<!-- That's all, folks! --> 




