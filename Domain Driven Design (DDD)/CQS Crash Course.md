
## Summary

Command Query Segregation is one of the primary patterns used in Altus’s Perifor code. This document will outline what it is and how it’s used.

## What is CQS

[CQS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation) is an architectural code pattern that separates code that modifies data from code that queries data. This is closely related to [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_Query_Responsibility_Separation) (Command Query Responsibility Segregation) a pattern that helps systems scale by adhering to the ACID (Atomicity, Consistency, Isolation, Durability) database model.

In practical terms implementing CQS means writing Command classes that performs an actions, and query classes return data. Primarily, queries should not have side effects (modify data).

## Why Use CQS

Adhering to this simple rule brings code closer to the ideal of [SRP](https://en.wikipedia.org/wiki/Single-responsibility_principle) (Single Responsibility Principle). Adding new features to a code base using CQS means adding new command or query classes. Existing code should be left untouched. Over time, this should result in less refactoring bugs.

Another benefit of this pattern is that it’s modeled as a pipeline. Behaviors can be added at the beginning or end of the code pipeline. This is great for addressing cross cutting concerns such as logging and validation at one point in the code.

Lastly, testing is simpler when the number of dependencies to code are kept few. Because the pattern helps us adhere to SRP, most command should have few dependencies. Queries are typically simple enough that testing is not justified.

## How is CQS Implemented?

Commands an queries are isolated into discrete chunks of code. This means this pattern is class heavy.  The code base will have small isolated classes that need to be callable from the outer layer of the application. The publish/subscribe and messaging patterns enable this to work. Enter [MediatR](https://github.com/jbogard/MediatR), a library that allows us to easily organize our application into feature slices.

Below is an example of a Command class using MediatR.

_Two classes are used. The first class is a message containing data, it uses the IRequest<> interface. This class is published in the system to be received by a matching request handler._

```
public class AddThingCommand : IRequest<Result<Guid>> {

        public Thing Model { get; }

        public AddThingCommand(Thing thing) {

            Thing = thing;

        }

 }
```


_Note that the Command Handler class uses the IRequestHandler<> interface, and takes as a generic parameter the type of request to be handled._

```
 public class AddThingCommandHandler : IRequestHandler<AddThingCommand, Result<Guid>> {

        private readonly IThingService _service;

        public AddThingCommandHandler(IThingService service) {

            _service = service ?? throw new ArgumentException(nameof(service));

        }

       public async Task<Result<Guid>> Handle(AddThingCommand request, CancellationToken cancellationToken) {

            var saved = _service.Add(request.Model);

            return new CreatedResult<Guid>(request.Model.Id);

        }

 }
```

This encapsulated code can now be called from anywhere within the application.

```
 var response = await Mediator.Send(new AddThingCommand(thingModel));
```

## Can MediatR be used incorrectly

Yes! As with most things, there are a few things to watch out for. Messaging is a neat trick, but it can make debugging harder to follow if you aren’t aware of what’s going on. One rule you’ll want to follow is ensuring that you only use this pattern at the edge of your application. The guts of code should not be messaging. Also, you should not create chains of commands. These become almost impossible to follow with async debugging.

## Further Reading

[CQS with Mediatr (andydote.co.uk)](https://andydote.co.uk/2016/03/19/cqs-with-mediatr/)

[CQRS with Mediatr and ASP.NET Core - Steve Gordon - Code with Steve (stevejgordon.co.uk)](https://www.stevejgordon.co.uk/cqrs-using-mediatr-asp-net-core)

[CQRS With MediatR In .NET 5 (c-sharpcorner.com)](https://www.c-sharpcorner.com/article/cqrs-mediatr-in-net-5/)