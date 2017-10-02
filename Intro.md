# Subsecond TDD

Subsecond TDD is an approach to building complex software, optimised for developer productivity and happiness.

## Fail fast first

There is one rule: don't let slow tests break your flow.

System tests typically simulate a production environment as accurately as possible to achieve the highest level of confidence in the correctness of the system under test.

But optimising for confidence can be an expensive choice. Real systems use slow and cumbersome components that are difficult to automate. System tests quickly become so slow and unreliable that they cannot practically drive the development process in the same way low-level unit tests can.

By structuring application and test code such that every slow component can be substituted with a fast equivalent, developers can run system tests fast for instant feedback, then the same tests in slow, fully-integrated mode for a final confirmation.

Like low-level TDD guides the development of individual units, Subsecond TDD guides the development of end-to-end features.

## Benefits

* **Confidence**

  Fast system tests make it faster to perform sweeping changes to the system under test. Unlike low-level tests they act as a safety net but not a straightjacket. Different modes of running tests offer unique assurances about different facets of the system under test.

* **Flow**

  Fast system tests act as a development time metronome, setting the rhythm. Immediately catching logical errors reduces the effort required to fix them and makes your slow tests less likely to fail. Increasingly integrated test modes guide the developer in small steps towards fully integrated features.

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

A rule of thumb is to avoid running any slow test (longer than 1 second) unless you already saw the exact same test passing fast (under one second). But the performance of this very first test is clearly determined by the speed of the components in scope: the browsers and databases.

You do some profiling and discover that the browser automation library takes 1.7 seconds to launch a browser window. That's too long to wait for feedback so you devise a way of running your single test with a different setup: you reconfigure the test to interact directly with the http server hosting your web app.

Running the same test without a browser takes 0.6 seconds. So now you have two ways of running the same system tests: one faster, one slower.

In your new fast mode, client-side script is not executed, and as you introduce more client-side code you lose confidence in your fast tests. So you devise a way of running the client-side script against a virtual browser DOM. Now you have three modes of running the same test.

After implementing more features the test suite starts to drag and you notice a delay of over a second between each change. So you substitute the database for an in-memory implementation of the Guestbook contract.

Now you have even more ways of running the same set of tests. When implementing new features you run the same tests 5 times:

* memory
* database
* http + memory
* virtual browser + memory
* browser + http + database

By running through the modes roughly in order of fastest to slowest, each mode guides the developer through the development of a change to a single layer of the system under test:

1. Adding behaviour in the domain model passes 'memory' tests
2. Implementing changes in the database passes 'database' tests
3. Implementing web app handlers passes 'http + memory'  tests
4. Adding client-side JavaScript passes 'virtual browser + memory' tests
5. The 'browser + http + database' tests pass without any changes.

Every time your tests get slow, you make them fast again.
