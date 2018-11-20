# AnySerializer

A CSharp library that can binary serialize any object quickly and easily. No attributes/decoration required!

That's right, no need for `[Serializable]` or any other custom attributes on your classes!

## Description

AnySerializer was built for software applications that make manual serialization difficult, or time consuming to decorate and design correctly. Other libraries require custom attributes to define serialization contracts, or fail at more complicated scenarios that involve interfaces, delegates and events defined. That's where AnySerializer shines! It literally is an anything in, anything out binary serializer.

## Installation
Install AnySerializer from the Package Manager Console:
```
PM> Install-Package AnySerializer
```

## Usage

```csharp
using AnySerializer;

var originalObject = new SomeComplexTypeWithDeepStructure();
var bytes = Serializer.Serialize();
var restoredObject = Serializer.Deserialize<SomeComplexTypeWithDeepStructure>(bytes);

```

### Ignoring Properties/Fields

Ignoring fields/properties is as easy as using any of the following standard ignores: `[IgnoreDataMember]`, `[NonSerializable]` and `[JsonIgnore]`. Note that `[NonSerializable]` only works on fields, for properties (and/or fields) use `[IgnoreDataMember]`.

### Providing custom type mappings

If you find you need to map interfaces to concrete types that are contained in different assemblies, you can add add custom type mappings:

```csharp
var originalObject = new SomeComplexTypeWithDeepStructure();
var bytes = Serializer.Serialize();

var typeMaps = TypeRegistry.Configure((config) => {
  config.AddMapping<ICustomInterfaceName, ConcreteClassName>();
  config.AddMapping<ICustomer, Customer>();
});

var restoredObject = Serializer.Deserialize<SomeComplexTypeWithDeepStructure>(bytes, typeMaps);
```

or alternatively, a type factory for creating empty objects:

```csharp
var originalObject = new SomeComplexTypeWithDeepStructure();
var bytes = Serializer.Serialize();

var typeMaps = TypeRegistry.Configure((config) => {
  config.AddFactory<ICustomInterfaceName, ConcreteClassName>(() => new ConcreteClassName());
});

var restoredObject = Serializer.Deserialize<SomeComplexTypeWithDeepStructure>(bytes, typeMaps);
```

and an alternate form for adding one-or-more mappings:

```csharp
var originalObject = new SomeComplexTypeWithDeepStructure();
var bytes = Serializer.Serialize();

var typeMap = TypeRegistry.For<ICustomInterfaceName>()
                .Create<ConcreteClassName>();

var restoredObject = Serializer.Deserialize<SomeComplexTypeWithDeepStructure>(bytes, typeMap);
```

or single type one-or-more factories:

```csharp
var originalObject = new SomeComplexTypeWithDeepStructure();
var bytes = Serializer.Serialize();

var typeMap = TypeRegistry.For<ICustomInterfaceName>()
                .CreateUsing<ConcreteClassName>(() => new ConcreteClassName());

var restoredObject = Serializer.Deserialize<SomeComplexTypeWithDeepStructure>(bytes, typeMap);
```

### Validating binary data

A validator is provided for verifying if a serialized object contains valid deserializable data that has not been corrupted:

```csharp
var originalObject = new SomeComplexTypeWithDeepStructure();
var bytes = Serializer.Serialize();
var isSuccess = Serializer.Validate(bytes);
Assert.IsTrue(isSuccess);
```

### Extensions

You can use the extensions to perform serialization/deserialization:

```csharp
using AnySerializer.Extensions;

var originalObject = new SomeComplexTypeWithDeepStructure();
var bytes = originalObject.Serialize();
var restoredObject = bytes.Deserialize<SomeComplexTypeWithDeepStructure>();
```

### Other applications

To see differences between two serialized objects you can use [AnyDiff](https://github.com/replaysMike/AnyDiff) on your copied object:

```csharp
using AnyDiff;

var object1 = new MyComplexObject(1, "A string");
var object1bytes = Serializer.Serialize(object1);
var object2 = Serializer.Deserialize<MyComplexObject>(object1bytes);

object2.Id = 100;

// view the changes between them
var diff = AnyDiff.Diff(object1, object2);
Assert.AreEqual(diff.Count, 1);
```

If you need a way to copy an object that doesn't involve serialization, try [AnyClone](https://github.com/replaysMike/AnyClone) which is a pure reflection based cloning library!
