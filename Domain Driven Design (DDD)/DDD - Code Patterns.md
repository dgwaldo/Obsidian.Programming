## The Creation of Aggregate Roots
[Don’t Create Aggregate Roots (udidahan.com)](https://udidahan.com/2009/06/29/dont-create-aggregate-roots/)

This guidance leans towards creating factory methods within your aggregates, which allow you create downstream aggregates. The pattern can be followed by avoiding the 'new' keyword in favor of first fetching an aggregate then calling it's factory method.

**Example Aggregate Factory:**
```
public class DivisionAddedCommand {
	...
	// This is the preferred pattern for generating new aggregate roots
	public void Handle(Model m){
		var company = _companyRepo.GetCompany(m.CompanyId);
		var division = company.AddDivision(m.DivisionInfo);
		_divisionRepo.Add(division);
	}
	
}
```


## State Records for Aggregate Roots
[DDD – The aggregate · Los Techies](https://lostechies.com/gabrielschenker/2015/05/25/ddd-the-aggregate/)

This pattern separtes the state of the aggregate, from the class containing the logic. Many examples show the aggregate simply as an entity where the properties of the class are persisted and the methods for invariant enforcement are within the class. 

**Example Model:**
```
public class Team {
	public Guid Id { get; set; }
	public string Name { get; set;}
	public IList<Player> Players { get; set; }
	public Coach Coach { get; set; }
	public Location HomeStadium { get; set; }
}
```

Given the above Team class, if you use an ORM such as EF, you can persist the entire object graph with the navigation properties. If you use a lightwigh ORM such as Dapper all the navigation properties will need persisted individually. This additional work will show up as more code in the aggregate repositories using lightweight ORMs.

This pattern is works very well with Dapper when additional data (the navigation properties) can be stored as JSON fields in the database, and act as value objects within the aggregate.

## Firing & Responding to Domain Events 
[Using Domain Events within a .NET Core Microservice - Cesar de la Torre (microsoft.com)](https://devblogs.microsoft.com/cesardelatorre/using-domain-events-within-a-net-core-microservice/)

Domain events are used within the same app domain to notify of things that have happened in the domain. One key reason to use domain events is that they help the code adhere to the SRP principle over time. New classes can be added that extend functionality in response to an event. 

* Domain Events should have past tense names, as they have occured.
* Domain Events should be handled in process (these are not integration level events)
	* Long running processes can be handed off to background workers (Hangfire)
	* They can trigger integration level events
* They can cause issues with the ACID ideals for database transactions

When using an ORM such as Entity Framework, the .Save method can be overriden to allow domain events to be processed before or after the actual database save. If all events are processed before save, the actual peristance to the database will be wrapped in a transaction and rolled back if anything goes wrong. If data is first persisted to the database, then there are chances that a failure to save a record can cascade into a larger transaction issue. 

When using a lightweight ORM such as Dapper, it becomes very hard to chain events and keep everything within one database transaction. It's more common then to find each record being persisted before other domain events are processed. The issue then becomes how to handle consistency and rollbacks.

When to use eventual consistency vs. transactional? The following is guidance from Vern Vaughn.

> Discussing this with Eric Evans revealed a very simple and sound guideline. When examining the use case (or story), ask whether it's the job of the user executing the use case to make the data consistent. If it is, try to make it transactionally consistent, but only by adhering to the other rules of aggregate. If it is another user's job, or the job of the system, allow it to be eventually consistent. That bit of wisdom not only provides a convenient tie breaker, it helps us gain a deeper understanding of our domain. It exposes the real system invariants: the ones that must be kept transactionally consistent. That understanding is much more valuable than defaulting to a technical leaning.

[domain driven design - DDD: deciding when to lean towards eventual vs transactional consistency - Software Engineering Stack Exchange](https://softwareengineering.stackexchange.com/questions/371945/ddd-deciding-when-to-lean-towards-eventual-vs-transactional-consistency)

[events - DDD dealing with Eventual consistency for multiple aggregates inside a bounded context with NoSQL - Stack Overflow](https://stackoverflow.com/questions/51773949/ddd-dealing-with-eventual-consistency-for-multiple-aggregates-inside-a-bounded-c)

## Value Objects

A building block of DDD. Value objects don't have an identifier field. Rather identity is determined by comparing the values stored in the field of the object. 

The C# 9 record looks like a good approximation for a value object, except it looks like it could cause some issues. Better off still using a class.

[C# 9 Records as DDD Value Objects · Enterprise Craftsmanship](https://enterprisecraftsmanship.com/posts/csharp-records-value-objects/)

## Working with Collections of Aggregate Roots (Aggregate bulk Updates)
[DDD and bulk operations · Enterprise Craftsmanship](https://enterprisecraftsmanship.com/posts/ddd-bulk-operations/)


## Enforcing invariants across aggregates

[c# - DDD - How to enforce invariants on collections across aggregates - Stack Overflow](https://stackoverflow.com/questions/60378094/ddd-how-to-enforce-invariants-on-collections-across-aggregates)


## Mapping Models

How to handle mapping of models from layer to layer?
[Is Layering Worth the Mapping? (ploeh.dk)](https://blog.ploeh.dk/2012/02/09/IsLayeringWorththeMapping/)






