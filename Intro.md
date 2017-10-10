# Sub-Second TDD

Sub-Second TDD is an approach to building software optimised for developer productivity and happiness. There is one rule: don't let slow tests break your flow.

## Fail fast first

Many developers use test-driven development (TDD) to building individual units of software in isolation. But TDD doesn't scale particularly well: high-level tests for complex systems (known as system tests and acceptance tests) are generally too slow and unreliable to practically drive the development process in the same way low-level unit tests do.

System tests and automated acceptance tests usually simulate a production environment as accurately as possible, in order to achieve the highest level of confidence in the correctness of the system under test. But optimising for confidence can be an expensive choice. Real systems use slow and cumbersome components that are difficult to automate. So high-level tests naturally become a maintenance burden: developers can only afford to run them infrequently, so they have many ways to break and are consequently hard to fix.

So how do we achieve the confidence of high-level tests, with the speed and resilience of low-level tests? The answer is to make the trade off between speed and confidence explicit and optimise the design of the system and its tests to yield as much confidence as possible within a fast feedback cycle. One second is the magic number for maintaining flow: after this duration human developers tend to drift off and lose focus on the task at hand.

## Honeycomb architecture

One approach to achieving Sub-Second TDD is a pattern known as the Honeycomb architecture. An extension of Hexagonal architecture, honeycomb is focused specifically on interchangeable components for the sake of fast and comprehensive feedback.

By structuring application and test code such that every slow component can be substituted with a fast equivalent, developers run a set of system tests really fast for instant feedback, then on a slower cycle run the exact same tests in fully-integrated mode for a final affirmation.

Like traditional TDD guides the development of individual units, honeycomb architecture can guide the development of end-to-end features in a systematic order.

## Benefits

* **Confidence**

  Fast "end-to-end" tests make it faster to perform sweeping changes to the system under test. Unlike low-level tests they act as a safety net but not a straightjacket. Different ways of running the same tests offer unique perspectives on the behaviour of the system under test.

* **Flow**

  Fast end-to-end tests act as a development time metronome, setting a rhythm. Immediately catching logical errors keeps the pace up, reducing the effort required to fix and making slow tests less likely to fail. Increasingly integrated test modes guide the developer in small steps towards fully integrated features.

* **Options**

  The architectural flexibility required to achieve sub-second feedback gives developers tools that allow attacking problems in a different order than they might with traditional TDD or BDD, depending on the risks imposed by the feature under development. It's not strictly outside-in, it's whatever works.

## Costs

* **More code**

  Substituting slow code with fast code means more code in total. Writing and maintaining that code takes time.

* **More indirection**

  Tests and system code are both more abstract and probably harder to follow.

## Who is it for?

Honeycomb architecture means frequently adapting the design of the complete system, so it only makes sense for competent programmers who are completely empowered to influence the architecture of the system under development.

## Starting from scratch

Let's say you've designed the first version of a guestbook app and you've settled on a database-backed web app architecture with some client-side code: an isomorphic single page web app, if you like. A fully-integrated high-level test would therefore:

1. Start a database server hosting an empty database
2. Start a web server hosting your web app
3. Launch a browser running your client-side code

Although these layers look very different, you notice that all three effectively satisfy the exact same role, from the perspective of any test:

    Guestbook
    * add visitor
    * list visitors

You write your first end-to-end test which adds a visitor to the guestbook. You run it against your full stack but then you realise it takes over 2 seconds!

A rule of thumb is to avoid running any slow test (longer than 1 second) unless you already saw the exact same test passing fast (under 1 second). But the performance of this very first test is clearly determined by the speed of the big things in scope: the browsers and databases.

You do some profiling and discover that the browser automation library takes 1.7 seconds to launch a browser window. That's too long to wait for feedback so you devise a way of running your single test with a different setup: you reconfigure the test to interact directly with the http server hosting your web app.

Running the same test without a browser takes 0.6 seconds. So now you have two ways of running the same system test: one faster, one slower.

In your new fast mode, client-side script is not executed, so after introducing more client-side code you lose confidence in your fast tests. You devise a way of running the client-side script against a virtual DOM, avoiding using a real browser. Now you have three modes of running the same test.

After implementing more features the test suite starts to drag and you notice a delay of over a second between each change. So you substitute the database for an in-memory implementation of the Guestbook contract.

Now you have even more ways of running the same set of tests. When implementing new features you run the same tests 5 times:

* memory
* database
* http + memory
* virtual browser + memory
* browser + http + database

By running through the modes roughly in order of fastest to slowest, each mode guides you towards a change to a single "layer" of the system under test:

1. Adding behaviour in the domain model passes 'memory' mode
2. Implementing changes in the database passes 'database' mode
3. Implementing web app handlers passes 'http + memory'  mode
4. Adding client-side JavaScript passes 'virtual browser + memory' mode
5. The 'browser + http + database' mode passes without any changes.

Every time your immediate tests slow down to 1 second, you do whatever it takes to speed them up.
