# Null Check Examples

Turns out, like a snowball, C# is adding all sorts of way to check for null. 

## Example 1

```csharp
if (thing != null){
	thing.Do();
}
```

## Example 2

[Via David Fowler](https://twitter.com/davidfowl/status/1421712013936369665)

[`is` operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/is)

```csharp
if (thing is object){
	thing.Do();
}
```

## Example 3

[Via David Fowler](https://twitter.com/davidfowl/status/1421712013936369665)

[`is` operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/is)

[`not` via negation pattern](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#logical-patterns)

```csharp
if (thing is not null){
	thing.Do();
}
```

## Example 4

[Via David Fowler](https://twitter.com/davidfowl/status/1421712013936369665)

[Property patterns](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#property-pattern) (I think)

```csharp
if (thing is {} t0}){
	thing.Do();
}
```

## Example 5

[Via David Fowler](https://twitter.com/davidfowl/status/1421712013936369665)

[Property patterns](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#property-pattern) (I think)

```csharp
if (thing is Thing t1}){
	thing.Do();
}
```

## Example 6

[Via David Fowler](https://twitter.com/davidfowl/status/1421717001412562950)
[Null conditional operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-)

```csharp
thing?.Do()
```