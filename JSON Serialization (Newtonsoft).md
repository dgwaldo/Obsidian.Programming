### Serializing Private Fields
When working with codebases adhering to DDD, it's common to create classes with private constructors and private fields. Serializing these to JSON can be problematic.  The below code shows how to get serialization of private fields working with Newtonsoft JSON.

A new contract resolver is needed that makes the private setter writable. Here we extend the CamelCasePropertyNamesContractResolver from the Newtonsoft library.

```
 public class PrivateSetterContractResolver : CamelCasePropertyNamesContractResolver {
        protected override JsonProperty CreateProperty(System.Reflection.MemberInfo member, MemberSerialization memberSerialization) {
            var prop = base.CreateProperty(member, memberSerialization);

            if (!prop.Writable) {
                var property = member as System.Reflection.PropertyInfo;
                if (property != null) {
                    var hasPrivateSetter = property.GetSetMethod(true) != null;
                    prop.Writable = hasPrivateSetter;
                }
            }

            return prop;
        }
    }
```

This contract resolver can then be used when configuring the JSON serializer.
```
var settings = new JsonSerializerSettings {
    ContractResolver = new PrivateSetterContractResolver(),
    ConstructorHandling = ConstructorHandling.AllowNonPublicDefaultConstructor
};
```