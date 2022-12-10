*Immutable types that are distinguishable only by the state of the properties.*

## Validation


## Creation Patterns
Value object creation is quite simple when a value object only has three or four properties. However, some value objects have several properties. The options are using a constructor, a factory method or the build pattern. If your approach to validation is to return error messages rather than throw exceptions, then the constructor doesn't work well. This leaves the factory and builder.

### Builder Pattern
This method works well when the fields needing to be set on the value object are few. The example below is complete, but only shows the setting of a few fields. Methods inputs can often be logically grouped such that each With... methods sets a few parameters at a time.

```
public class Package : ValueObject {

	private ValidationErrors _errors;
	
	private Package() {
	    _validation = new ValidationErrors();
	}
	
	public Address Address { get; private set; }
	public string Contents { get; private set; }
	public BoxDim Dimensions { get; private set; }
	public double WeightLbs { get; private set; }
	
	public static Package Create(){
		return new Package();
	}

	public Package WithAddress(Address address){
		Address = address;
		return this;
	}
	
	public Package WithContentsOf(string contents){
		if(String.IsNullOrEmpty(contents)){
			_errors.AddError("Package contents must not be empty");
		}
		Contents = contents;
		return this;
	}

	public Package WithDimensions(boxDim dimensions){
		BoxDim = boxDim;
		return this;
	}

	public Package WithWeight(double WeightLbs){
		if(weightLbs <= 0){
			_errors.AddError("Yah right, it has to weigh something");
		}
		WeightLbs = weightLbs;
		return this;
	}

	public Either<ValidationErrors, Package> Build() {
		if (_validation.HasErrors()) {
			return _validation;
		}
		return new Package {
			Address = Address,
			Contents = Contents,
			Dimensions = Dimensions,
			WeightLbs = WeightLbs
		};
    }
	
}
```

### Static Factory Method
When inputs into the value object are shorter, using a static create method that accepts all the parameters works well. 

```
 public class UnitDefinition {

    public const string Unit_Abbreviation_Required = "Unit abbreviation is required";
    public const string Unit_Name_Required = "Unit name is required";
      
    private UnitDefinition() { }

	private UnitDefinition(string? abbreviation, string? name) {
		Abbreviation = abbreviation 
			?? throw new ArgumentNullException(nameof(abbreviation));
		Name = name 
			?? throw new ArgumentNullException(nameof(name));
	}

	public static Either<ValidationErrors, UnitDefinition> Create(
		string? abbrev, 
		string? name
		) {
		var errors = new ValidationErrors();

		if (string.IsNullOrEmpty(abbrev)) {
			errors.AddError(Unit_Abbreviation_Required);
		}

		if (string.IsNullOrEmpty(name)) {
			errors.AddError(Unit_Name_Required);
		}

		if (errors.HasErrors()) {
			return errors;
		}

		return new UnitDefinition(abbrev, name);
	}

	public string Abbreviation { get; private set; }

	public string Name { get; private set; }
}
```

## Persistence Strategy

Value objects can be persisted in SQL as JSON. This works well when aggregates contain several value objects, as they can be persisted in the same record. Also, collections of value objects can be handled this way.  The only issue is with serialization due to the private setters. This can be worked around. See [[JSON Serialization (Newtonsoft)]]