Lego bricks have studs on the top and tubes at the bottom. The studs fit into the tubes, and this allows bricks to be stacked on top of each other.

*Illustration showing 2 lego bricks on top of each other*

This design principle can be applied to layers of an application as well as automated tests. The studs and tubes represent domain-specific operations. More specifically, the studs represent *functions* and the tubes represent objects or other functions *invoking* the stud functions. Another way to think of it is that the top of a lego represents an *interface* and the bottom represents objects or functions *depending* on that interface.

*Illustration showing a UI connected to a model. UI interface looks like buttons and fields. Model interface has circles labelled with method names*.

This allows unit testing a UI component with a fake model. 

In the world of Lego there is only one interface, and a brick's studs are the inverse of its tubes. Software component are typically different. Their studs are not compatible with their tubes.

For any given component's input and output interface we can can connect an inverse component. Connected together, these two component now have the same property as a lego brick. Its studs are the inverse of its tubes.

Component pairs like this can be assembled in various ways:

*Illustration showing 2 assemblies: UI+fake model, UI+model+fake orm+, UI+model+real orm+databse

We can make the component interfaces uniform by inserting inverse components in-between. These components simply translate from one protocol to another. From domain operation to HTTP, and from HTTP back to domain operation. From domain operation to ORM call, and down to the database. The latter is a leaf component, as it does not make sense to connect it to an inverse.

An interesting opportunity arises when we connect a domain-specific component whose output interface is the same as the UI component's input interface. Now we can write acceptance tests against the domain interface. We can run those tests agains different assemblies of our components:

*Illustration showing Cucumber->[DomApi->Dom]->[HTTPApi->HTTPRequest]->[HTTPRequest->RequestHandler]->[DomainApi->DAO]->[DB]*

We can connect Cucumber to [HTTPApi] instead, and trade lower confidence for higher speed. Being able to make this tradeoff
is essential to the sub-second TDD workflow. It encourages programmers to concentrate domain logic in a component that can be completely decoupled from technical infrastructure. This enables fast tests that are only CPU bound and not IO bound. Programmers can iterate very fast on domain logic changes.

It also allows for very flexible and iterative development. Sometimes it makes sense to start the TDD process near the UI, if that's the part which is least known. This allows for consumer-driven contracts, which we believe lead to good design.
Other times we know the downstream constraints and choose to start there. We try to avoid drilling tunnels.

To take advantage of this approach it must be easy to assemble the application, and also understand what goes into each assembly. A functional approach might work here, where functions are composed to connect components. For example:

```javascript
let todo
beforeEach(() => {
  if(process.env.todo_stack === 'full') {
    // full-stack test
    todo = SeleniumTodo(Dom, HttpTodo(DomainTodo(ORM(DB))))
  else if(process.env.todo_stack === 'http') {
    // http API test
    todo = HttpTodo(DomainTodo(MemoryORM))
  } else {
    // in-memory (Cucumber-Electron's DOM makes DomTodo available directly)
    // We also skip the HTTP layer
    todo = DomTodo(Dom, DomainTodo(MemoryORM))
  }
)
it('should add a todo item', () {
  app.addItem({text: 'buy milk'})
  assert.deepEqual(app.getItem(0), {text: 'buy milk'})
})
```
