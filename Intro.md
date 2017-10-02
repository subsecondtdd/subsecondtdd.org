# Subsecond TDD

Running system tests fast

## Philosophy

* System tests inspire system-wide confidence.
* System tests are decoupled and resilient to changes.
* Tests aren't slow, they have slow collaborators.
* Fast system tests afford fewer unit tests.
* Run system tests fast.

## Fast system tests

Subsecond TDD generates sustainable system tests by insisting they run fast.

System tests typically simulate a production environment as accurately as possible to achieve the highest level of confidence in the correctness of the system under test.

But optimising for confidence can be an expensive choice. Real systems use slow and cumbersome components that are difficult to automate. System tests quickly become so slow and unreliable that they cannot practically drive the development process in the same way low-level unit tests can.

By structuring application and test code such that each slow component can be substituted with a fast equivalent, developers can run system tests fast, then slow.

Fast system tests can effectively guide the development of end-to-end features.

## Benefits

* **Confidence**
  
  Fast system tests make it practical to perform sweeping changes to the system under test with a safety net but not a straightjacket. Different ways of running tests offer unique assurances about different aspects of the system under test.

* **Flow**

  Fast system tests act as a development-time metronome, setting the rhythm and immediately catching logical and programmer errors. Increasingly integrated test modes guide the developer in small steps towards fully integrated features.

* **Options**

  The architectural flexibility required to achieve sub-second feedback gives developers tools that allow solving problems in a different order than they might with traditional TDD or BDD, depending on the risks imposed by the feature under development.

## Costs

* **More code**

  Substituting slow code with fast code means more code in total. Writing and maintaining that code takes time.

* **More indirection**

  Tests and system code are both more abstract and potentially harder to follow.

## Who is it for?

Subsecond TDD is for competent programmers who are completely empowered to influence the architecture of the system under development.

## Subsecond TDD is not...

Subsecond TDD is not like traditional TDD because it isn't focused on units like modules or functions.

Subsecond TDD is not exactly like BDD or acceptance testing because it's agnostic about language and syntax. It is achievable with any technology stack and test runner that can run a test in under one second.

Subsecond TDD is not a silver bullet.

## Starting from scratch

Let's say you've designed the first version of a guestbook app and you've settled on a database-backed web app with some client side code. A system test would therefore:

1. Start a database server hosting an empty database
2. Start a web server hosting your web app
3. Launch a browser running your client-side code

Although these look very different you notice that all three components effectively satisfy the exact same role, from the perspective of any test:

    Guestbook
    * add visitor
    * list visitors

You write your first system test which adds a visitor to the guestbook. You run it against your full stack but then you realise it takes over 2 seconds!

A rule of thumb with sub-second feedback is that you should avoid running any slow test (more than 1 second) unless you already saw the exact same test passing fast (under one second). But the performance of this very first test is determined by the speed of the components under test.

You do some profiling and discover that the browser automation library takes 1.7 seconds to launch the browser window, so you devise a way of running the your single test but reconfigured to interact directly with the http server hosting your web app.

Running the same test at a lower-level, without the browser, run in 0.6 seconds. So now you have two ways of running the same system tests: one fast but running slightly less of your code, and one slow running more of it.

In fast mode, the client-side script is out of scope, and as you add more code there your confidence in fast tests is reduced. So you devise a way of running the client-side script against a virtual browser DOM. Now you have three modes of running the same test.

After implementing more features the test suite starts to drag and you notice a delay of over a second between each change. So you substitute the database for a new, in-memory implementation of the Guestbook contract.

Now you have even more ways of running the same set of tests. When implementing new features you run the same tests 5 times:

* memory
* database
* http + memory
* virtual browser + memory
* browser + http + database

By running them roughly in order of fastest to slowest, each modes guides you through the development of a change to a single "layer" of the system under test:

1. Adding behaviour in the model passes 'memory' mode tests
2. Implementing changes in the database passes 'database' mode tests
3. Implementing web app handlers passes 'http + memory'  mode tests
4. Client-side JavaScript passes 'virtual browser + memory' mode tests
5. The 'browser + http + database' mode tests pass without any changes.

Every time your tests get slow, you make them fast again.
