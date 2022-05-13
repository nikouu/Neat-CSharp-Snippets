# Checking Is a String Is Null or Empty Examples

## Example 1
```csharp
string.IsNullOrEmpty(str);
```

## Example 2
[Via Bartosz Adamczewski](https://twitter.com/badamczewski01/status/1449319543336222725)
```csharp
str is not { Length: > 0 };
```

## Example 3
[Via Bartosz Adamczewski](https://twitter.com/badamczewski01/status/1449319543336222725)
```csharp
str is null or "";
```

## Example 4
[Via @elhipernauta](https://twitter.com/elhipernauta/status/1449352457549713415)
```csharp
str is null or { Length: 0 }
```