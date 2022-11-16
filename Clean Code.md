## The Magical Number Seven, Plus or Minus Two 
George Miller

>The Magic number 7 (plus or minus two) provides evidence for the capacity of short term memory. Most adults can store between 5 and 9 items in their short-term memory.  This idea was put forward by Miller (1956) and he called it the magic number 7

## Checklists
- Warnings
- Analyzers 
- Release Checklists
	- Restore 
- Sprint Checklists
	- Update dependencies
- Security

## Tests
- Unit Tests
- Integration Tests
- UI Tests

## Nullability
JSON offers no gaurentee that data coming in on properties will not be null. It can be null. Thus, it likely makes sense to mark all properties on DTO's as nullable.

```
public class ReservationDto
{
	public string? At { get; set; }
	public string? Email { get; set; }
	public string? Name { get; set; }
	public int Quantity { get; set; }
}
```

## Immutability
Prefer immutable objects that garuntee the state isn't changing. This makes systems much easier to debug and they are threadsafe by default.

## Thresholds 
Create a set of maximums that if exceeded trigger a discussion on refactoring a block of code. Use linteres where possible.

	- 120 Characters per row
	- No more than 40 lines per method
	- No more than 7 variables per method
	- No more than 4 Dependencies per class

## Postel's Law
Be liberal in what you accept for input, but strict in output. [Robustness principle - Wikipedia](https://en.wikipedia.org/wiki/Robustness_principle)

## Be careful with ORM's 
[Object-Relational Mapping is the Vietnam of Computer Science (codinghorror.com)](https://blog.codinghorror.com/object-relational-mapping-is-the-vietnam-of-computer-science/)

## Feature Flags over branches
Prefer feature flags to long lived feature branches. This way code stays up to date on the mainline.

## Strangler Pattern for Complex Refactoring
Side by side change instead of inplace changes.

Use method overloads to change contracts within the codebase. First add the new method just to the affected class. Then switch code to use the new method as you can, leaving all of the old method calls. Once the change is made, remove the original old method, and finally add it back to the interface.  Finally pull the old interface definitions.

Take small, safe steps that can be frequently commited back to main. 

Another pattern that may be effective is creating a new class and inheriting the new class from the class needing to be replaced. 

## Return Clean Generics
Avoid writing methods that return nested generics type definitions.

This is less desirable.
IEnumerable<Thing<IEnumerbale>>

This is more desirable.
IEnumerable<Thing>
