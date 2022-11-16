
## Summary

 An Aggregate in DDD is a cluster of domain objects treated as a single unit. Aggregates should keep related data and business rules cohesive. They form a boundary around things that need to change together. The ubiquitous example being an Order and its corresponding Line Items. Business logic should dictate what falls inside an aggregate boundary. 


 Large cluster aggregates with many child objects will cause transactional consistency issues. Small aggregates don't offer any benefit over a standard CRUD pattern app.

 This is a brief guide through the key principles of designing aggregates. These are guidelines, not law. They work together to reduce the probability of bad design.

REF: [Effective Aggregate Design | Kalele](https://kalele.io/effective-aggregate-design/) (Three papers) good read.

## Rules

Vaugh Vern lays out the 4 rules of aggregate design in his book [Domain Driven Design Distilled](https://www.amazon.com/Domain-Driven-Design-Distilled-Vaughn-Vernon/dp/0134434420)

1.       Within an aggregate boundary protect the business rules

2.       Keep the aggregates relatively small

3.       Reference other aggregates only by identity (Id)

4.       Other aggregates should be updated eventually

## Protect the Business Rules - [Invariants](https://www.i2p.es/arch/ddd/business-invariants/)

Aggregates as a concept are useful to simplify business requirements. They are an object-oriented way to codify the business rules. As such aggregates should adhere to the Single Responsibility Principle (SRP). It should contain only the data necessary to fulfill its designed purpose.

Aggregates may sometimes need data from other aggregates. There are many approaches to handling this. As a guide, in simple scenarios it may be adequate to use rule 4 (eventual consistency) to duplicate bits of data. More complex operations may need a domain service to load other aggregates to get the data.

Aggregates should prevent their internal state from becoming invalid. Methods within aggregate classes should prevent setting of invalid state, and ideally report any warnings/errors back to the consumer. Aggregates should be in a valid state before they are persisted back to the database.

Ref: [Microsoft Word - Immutability-Changes-Everything-141121a.docx (cidrdb.org)](http://www.cidrdb.org/cidr2015/Papers/CIDR15_Paper16.pdf)

## Small Aggregates

Small aggregates off many advantages over large cluster aggregates. They are more likely to be SRP compliant. Unit testing will be easier. Also, the memory footprint will be small. Finally, when saving to the database, concurrency won't be an issue.

### How to Design Small Aggregates

Start with the business concepts that seem to go together. Limit the root aggregate to one entity to begin with. Add all the fields needed for a valid initial state of the single root entity, as well as a way to find the aggregate through its identity.

Look at all of your other aggregates and see if any of them need to be updated in reaction to any event that occurs in the aggregate you’re designing. List out the other aggregates that will need to be updated.

Determine how much time can elapse before the event driven updates can take place. Immediately, seconds, hours etc. If any other entity or aggregate needs to be “immediately” updated, then consider moving it into the consistency boundary of your aggregate.

All other time frames other than immediate should be set to update using eventual consistency.

## Reference Other Aggregates by Identity

A key strategy to keep aggregates small is to never hold reference to other aggregates. When an aggregate loads, it should never load a second aggregate inside its boundary. It is acceptable to hold an Id to another aggregate. A domain service could use this Id to coordinate the loading and work of more than one aggregate.

Ref: [Rule: Reference Other Aggregates by Identity | Implementing Domain-Driven Design: Aggregates | InformIT](https://www.informit.com/articles/article.aspx?p=2020371&seqNum=4)

## Eventual Consistency

Update other aggregates eventually. Eventual consistency takes a little different approach that traditional CRUD based systems. It is the idea that parts of the domain can be saved milliseconds, seconds, minutes, hours or days later. All of course depending on the business needs. Aggregates can use events to notify other parts of the domain that something has occurred. The events trigger responding actions that may update data in other aggregates or run calculations etc. Eventually all the data will be consistent…

Ref: [Memories, Guesses, and Apologies - PatHelland's WebLog - Site Home - MSDN Blogs (archive.org)](https://web.archive.org/web/20100722052618/http:/blogs.msdn.com/b/pathelland/archive/2007/05/15/memories-guesses-and-apologies.aspx)

Ref: [Race Conditions Don’t Exist (udidahan.com)](https://udidahan.com/2010/08/31/race-conditions-dont-exist/)