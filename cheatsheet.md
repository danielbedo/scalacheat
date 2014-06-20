
## Scala Days Cheat Sheet ##
### variables and functions

    val x = 1                                       -- constant
    var y = 2                                       -- variable
    def example = 2                                 -- evaluated when called
    val example = 2                                 -- evaluated immediately
    lazy val example = 2                            -- evaluated once when needed
    def f(x: Int) = { x*x }                         -- define function
    def f(x: R)                                     -- call by value
    def f(x: => R)                                  -- call by name
    (1 to 5).map(_*2)                               -- anonymus function (positionally matched arguments)
    (1 to 5).map(2*)                                -- anonymus function (bound infix method)
    
    def add(x:Int) = (y:Int) => x + y               -- currying
    
    def mapmake[T](g:T=>T)(seq: List[T]) =          -- generic type
        seq.map(g)                              
    
    def sum(args: Int*) = args.reduceLeft(_+_)      -- varargs
    
    def sum(f: Int => Int): (Int, Int) => Int = {   -- returns a function that takes two integers and returns an 
      def sumf(a: Int, b: Int): Int = {...}            an integer 
      sumf  
    } 
    
### packages

    import scala.collection.                    -- wildcard import
    import scala.collection.{Vector, Sequence}  -- selective import
    import scala.collection.{Vector => Vec28}   -- renaming import
    import java.util.{Date => _, _}             -- import all except

### data structures
    (1,2,3)                                     -- Tuple3
    var (x,y,z) = (1,2,3)	                    -- destructuring bind: 
                                                   tuple unpacking via pattern matching.
    var xs = List(1,2,3)                        -- List
    xs(1)                                       -- List indexing
    1 :: List(2,3)                              -- cons
    ()	                                        -- sole member of the Unit type
    
### control constructs
    if (condition) dothis else dothat           -- if then else.
    while (x < 10) { println(x); x += 1}        -- while loop
    do { println(x); x += 1} while (x < 5)      -- dowhile loop
    for (x <- xs if x%2 == 0) yield x*10        -- for comprehension
    for (i <- 1 to 5) {                         -- for loop
        println(i)
    }

### pattern matching
    x match {                                   -- pattern matching
        case 1 => "one"
        case 2 => "two"
        case _ => "many"
    }
     
    (list: List[T]) match {                     -- pattern matching on lists
      case Nil => ...                           -- empty list
      case x :: Nil => ...                      -- list with only one element
      case List(x) => ...                       -- same as above
      case x :: xs => ...                       -- x is bound to the head, xs to the tail. 
      case (x, y) :: ps => ...                  -- a list where the head element is a pair
      case _ => ...                             -- default case if none of the above matches
    }
    
### object orientation

    class C(x: R)                               -- class with private member
    class C(val x: R)                           -- class with public member
    new{ ... }                                  -- anonymus class
    abstract class D { ... }                    -- abstract class
    class C extends D { ... }                   -- inheritance
    class C(x: R) extends D(x)                  -- inheritance and constructor params
    object O extends D { ... }	                -- singleton object
    trait T { ... }                             -- interfaces with implementation
    class C extends T { ... }
    class C extends D with T { ... }
    trait T1; trait T2                          -- multiple trait inheritance
    class C extends T1 with T2
    x.isInstanceOf[String]	                    --type check (runtime)
    x.asInstanceOf[String]                      -- type cast (runtime)
    
### type parameters

    def myFct[T <: TopLevel](arg: T): T = { ... }   -- T must derive from TopLevel or be TopLevel
    def myFct[T >: Level1](arg: T): T = { ... }     -- T must be a supertype of Level1
    def myFct[T >: Level1 <: Top Level](arg: T): T  -- both
    
### variance
Given **A <: B**
1, If **C[A] <: C[B]**, **C** is covariant
2, If **C[A] >: C[B]**, **C** is contravariant

    class C[+A] { ... }                             -- C is covariant
    class C[-A] { ... }                             -- C is contravariant
    class C[A]  { ... }                             -- C is nonvariant

### Operations on sequences
    xs.length               -- number of elements
    xs.last                 -- last element (exception if xs is empty)
    xs.init                 -- all elements of xs but the last (exception if xs is empty)
    xs take n               -- first n elements of xs
    xs drop n               -- the rest of the collection after taking n elements
    xs(n)                   -- the nth element of xs
    xs ++ ys                -- concatenation,
    xs.reverse              -- reverse the order
    xs updated(n, x)        -- same list than xs, except at index n where it contains x
    xs indexOf x            -- the index of the first element equal to x (-1 otherwise)
    xs contains x           -- same as xs indexOf x >= 0
    xs filter p             -- returns a list of the elements that satisfy the predicate p
    xs filterNot p          -- filter with negated p 
    xs partition p          -- same as (xs filter p, xs filterNot p)
    xs takeWhile p          -- the longest prefix consisting of elements that satisfy p
    xs dropWhile p          -- the remainder of the list after any leading element satisfying p have been removed
    xs span p               -- same as (xs takeWhile p, xs dropWhile p)
    xs exists p             -- true if there is at least one element for which predicate p is true
    xs forall p             -- true if p(x) is true for all elements
    xs zip ys               -- returns a list of pairs which groups elements with same index together
    xs unzip                -- opposite of zip: returns a pair of two lists
    xs.flatMap f            -- applies the function to all elements and concatenates the result
    xs.sum                  -- sum of elements of the numeric collection
    xs.product              -- product of elements of the numeric collection
    xs.max                  -- maximum of collection
    xs.min                  -- minimum of collection
    xs.flatten              -- flattens a collection of collection into a single-level collection
    xs groupBy f            -- returns a map which points to a list of elements
    xs distinct             -- sequence of distinct entries (removes duplicates)
    x +: xs                 -- creates a new collection with leading element x
    xs :+ x                 -- creates a new collection with trailing element x
    
### Operations on maps
    val aMap = Map("a" -> 1, "b" -> 5, "c" -> 10)   -- create a map
    myMap("a")                                      -- => 1  
    myMap("d")                                      -- => java.util.NoSuchElementException  
    myMap get "d"                                   -- => None 
    myMap get "a"                                   -- => Some(1)
    
### Operations on streams
    val xs = Stream(1, 2, 3)                                                -- create a stream
    val xs = Stream.cons(1, Stream.cons(2, Stream.cons(3, Stream.empty)))   -- same as above
    (1 to 1000).toStream                                                    -- => Stream(1, ?)
    x #:: xs                                                                -- Same as Stream.cons(x, xs)


### Monads
A monad is a parametric type M[T] with two operations: flatMap and unit.

    trait M[T] {
        def flatMap[U](f: T => M[U]) : M[U]
        def unit[T](x: T) : M[T]
    }
These operations must satisfy three important properties:

 1. **Associativity**: `(x flatMap f) flatMap g == x flatMap (y => f(y) flatMap g)` 
 2. **Left unit**: `unit(x) flatMap f == f(x)` 
 3. **Right unit**: `m flatMap unit == m`

### Try

    abstract class Try[T]
    case class Success[T](elem: T) extends Try[T]
    case class Failure(t: Throwable) extends Try[Nothing]

### Future

    trait Future[T] {                                           
        def onComplete(callback: Try[T] => Unit)                -- takes a method, callback to be called as soon as                                                                 -- the value of current Future is available. 
        (implicit executor: ExecutionContext): Unit
    }
    object Future {
        def apply(body: => T)(implicit context: ExecutionContext): Future[T]    -- starts an asynchronous computation                                                                                          -- in current context, returns a 
                                                                                -- Future object
    }
    futureValue onComplete {
      case Success(result) => result                                            -- if the Future succeeded
      case Failure(t) => println("error: " + t.getMessage)       -- if it failed
    }

### Promises

    trait Promise[T]
      def future: Future[T]
      def complete(result: Try[T]): Unit                        -- when the promise is completed
      def tryComplete(result: Try[T]): Boolean
    }

##### sources: 
> http://docs.scala-lang.org/cheatsheets/
> https://github.com/lampepfl/progfun-wiki/blob/gh-pages/CheatSheet.md
> https://github.com/sjuvekar/reactive-programming-scala/blob/master/ReactiveCheatSheet.md
