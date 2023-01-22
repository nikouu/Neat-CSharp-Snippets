# Neat C# Snippets
C# snippets that make me say "this will make a fine addition to my collection".

## Switch Expressions
See my repo [Switch Expression Examples](https://github.com/nikouu/Switch-Expression-Examples) for gathered code snippets.

## Generating Random with `Random.Shared.Next()`
Just a quick way to get a normal random number. [Documentation](https://docs.microsoft.com/en-us/dotnet/api/system.random.shared?view=net-6.0) and [initial proposal](https://github.com/dotnet/runtime/issues/43887).

```csharp
Random.Shared.Next()
```
## String is null or empty checks

### Example 1
The usual for comparison.
```csharp
string.IsNullOrEmpty(str);
```

### Example 2
[Via Bartosz Adamczewski](https://twitter.com/badamczewski01/status/1449319543336222725)
```csharp
str is not { Length: > 0 };
```

### Example 3
[Via Bartosz Adamczewski](https://twitter.com/badamczewski01/status/1449319543336222725)
```csharp
str is null or "";
```

### Example 4
[Via @elhipernauta](https://twitter.com/elhipernauta/status/1449352457549713415)
```csharp
str is null or { Length: 0 }
```

## Null Checks

Turns out, like a snowball, C# is adding all sorts of way to check for null. 

### Example 1

```csharp
if (thing != null){
	thing.Do();
}
```

### Example 2

[Via David Fowler](https://twitter.com/davidfowl/status/1421712013936369665)

[`is` operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/is)

```csharp
if (thing is object){
	thing.Do();
}
```

### Example 3

[Via David Fowler](https://twitter.com/davidfowl/status/1421712013936369665)

[`is` operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/is)

[`not` via negation pattern](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#logical-patterns)

```csharp
if (thing is not null){
	thing.Do();
}
```

### Example 4

[Via David Fowler](https://twitter.com/davidfowl/status/1421712013936369665)

[Property patterns](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#property-pattern) (I think)

```csharp
if (thing is {} t0}){
	thing.Do();
}
```

### Example 5

[Via David Fowler](https://twitter.com/davidfowl/status/1421712013936369665)

[Property patterns](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#property-pattern) (I think)

```csharp
if (thing is Thing t1}){
	thing.Do();
}
```

### Example 6

[Via David Fowler](https://twitter.com/davidfowl/status/1421717001412562950)
[Null conditional operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-)

```csharp
thing?.Do()
```

### C# 10 parameter null checking
`ArgumentNullException.ThrowIfNull()` that we can put at the top of a function to check incoming parameters. 
 ```csharp
ArgumentNullException.ThrowIfNull(config);
```

### Constructor inline null argument checking
When doing work understanding [System.CommandLine](https://github.com/dotnet/command-line-api) I saw [this type of pattern in the constructor](https://github.com/dotnet/command-line-api/blob/d4cdec2975ece784e6482abbfcf78a0c9516c499/src/System.CommandLine.Hosting/InvocationLifetime.cs#L34):

```csharp
public void ObjectConstructor(string config){
    _config = config ?? throw new ArgumentNullException(nameof(config));
    // ...
}
```

### Variable null check at assigning
I've seen it around, but this [tweet by David Fowler reminded](https://twitter.com/davidfowl/status/1540889628181499904) me to put it here.
```csharp
var connectionString = builder.Configuration.GetConnectionString("App") ??
    throw new InvalidOperationException("Missing connection string.")
```

## String Splitting

These examples are beyond the usual `string.split()` calls. 

### Example 1
[Via Guilherme Ferreira](https://twitter.com/gsferreira/status/1522628059056254979)

Using [ranges](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-8.0/ranges).

```csharp
var startIndex = text.IndexOf('-') + 1;
var endIndex = text.LastIndexOf('-') - startIndex;

var lastPartSubstring = text[startIndex..];
var middleSubstring = text[startIndex..endIndex];
```

## HttpClient
There's a lot of work around `HttpClient` and how to make it run *fast*. As of writing this, the newer [`IHttpClientFactory`](https://learn.microsoft.com/en-us/dotnet/core/extensions/httpclient-factory) class which does neat magic to address some of the downsides of just `HttpClient`. For the sake of this snippet, we'll just look at some quick drop-in speed improvements.

### Example 1
[Via Tugberk Ugurlu](https://www.tugberkugurlu.com/archive/efficiently-streaming-large-http-responses-with-httpclient)
[Via John Thiriet](https://johnthiriet.com/efficient-api-calls/)

Using `ReadAsStreamAsync()` and using your JSON converter on that stream instead of `ReadAsStringAsync()` then deserializing the JSON to an object.

```csharp
using var response = await client.SendAsync("http://localhost:3000");
var stream = await response.Content.ReadAsStreamAsync();

var deserializedObject = await JsonSerializer.DeserializeAsync<YourDTO>(stream);
return deserializedObject;
```

### Example 2
[Via Tugberk Ugurlu](https://www.tugberkugurlu.com/archive/streaming-with-newnet-httpclient-and-httpcompletionoption-responseheadersread)
[Via John Thiriet](https://johnthiriet.com/efficient-api-calls/)

Using [`HttpCompletionOption.ResponseHeadersRead`](https://learn.microsoft.com/en-us/dotnet/api/system.net.http.httpcompletionoption?view=net-7.0) with `HttpClient` which does:
> The operation should complete as soon as a response is available and headers are read. The content is not read yet.
This means the content isn't pre-buffered.

```csharp
using var response = await client.SendAsync("http://localhost:3000", HttpCompletionOption.ResponseHeadersRead);
var stream = await response.Content.ReadAsStreamAsync();

var deserializedObject = await JsonSerializer.DeserializeAsync<string>(stream);
return deserializedObject;
```
This is faster than Example 1, but understand what you're in for.

## Images
There's a reason it's normal to use a NuGet package for image processing in C#. But, if it's quick and dirty, the following examples will do just fine. These are Windows specific APIs.

### Crop Image (slow)

```csharp
for (int i = 0; i < image.Width; i += 8)
    {
        for (int j = 0; j < image.Height; j += 8)
        {
            var cloneRectangle = new Rectangle(i, j, 8, 8);
            using var tile = image.Clone(cloneRectangle, image.PixelFormat);
			// work with tile
        }
    }
```

### Crop Image (fast)
[Via Fopedush](https://stackoverflow.com/questions/9688454/cropping-an-area-from-bitmapdata-with-c-sharp/9691388#9691388)

THis example uses the above link has been applied to crop and save every 8x8 tile in an image.
```csharp
public void LoadImage(Bitmap image)
{
    // https://stackoverflow.com/a/9691388
    var rawImage = image.LockBits(new Rectangle(0, 0, image.Width, image.Height), ImageLockMode.ReadOnly, image.PixelFormat);
    var imageByteCount = rawImage.Stride * rawImage.Height;
    var imageBytes = new byte[imageByteCount];

    var croppedBitmap = new Bitmap(8, 8);

    Marshal.Copy(rawImage.Scan0, imageBytes, 0, imageByteCount);

    for (int i = 0; i < image.Width; i += 8)
    {
        for (int j = 0; j < image.Height; j += 8)
        {
            var croppedBytes = GetCroppedImage(imageBytes, rawImage.Stride, i, j, 8, 8);
            var croppedData = croppedBitmap.LockBits(new Rectangle(0, 0, 8, 8), ImageLockMode.WriteOnly, image.PixelFormat);

            Marshal.Copy(croppedBytes, 0, croppedData.Scan0, croppedBytes.Length);

            croppedBitmap.UnlockBits(croppedData);

            _tiles[i / 8, j / 8] = new Tile(croppedBitmap, i / 8, j / 8);
        }
    }

    image.UnlockBits(rawImage);
}
```

## Misc.

### Neat pattern matching
[Via Nick Craver](https://twitter.com/Nick_Craver/status/1508068445644017667)

[For another example, I use it here](https://github.com/nikouu/pokesprite-spritesheet/blob/main/PokespriteGenerator/Decompressor.cs#L25)

Here we use pattern matching to get our variable at the same time as we `while`. And it compliles to the same thing as we would've traditionally done it.
```csharp
while(reader.ReadLine() is string line) 
{
	// work
}
```
