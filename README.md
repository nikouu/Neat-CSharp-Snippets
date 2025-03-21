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

[Difference between `is not null` and `!= null` via Marc Gravell](https://twitter.com/marcgravell/status/1652270953143975938)

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

### Nullable bool checks 1
[Via Mackinnon Buck](https://twitter.com/MackinnonBuck/status/1648856449592987650)

His opinion: *looks confusing and requires parentheses if more conditions are added.*
```csharp
if (Model?.BoolProperty ?? false) { }
```

### Nullable bool checks 2
[Via Mackinnon Buck](https://twitter.com/MackinnonBuck/status/1648856449592987650)

His opinion: *looks trivially reducible at a glance, but isn't*. (Most popular in given poll)

Note: We're using `== true` because `BoolProperty` is a `bool?`
```csharp
if (Model?.BoolProperty == true) { }
```

### Nullable bool checks 3
[Via Mackinnon Buck](https://twitter.com/MackinnonBuck/status/1648856449592987650)

His opinion: *has a bunch of syntax.*
```csharp
if (Model is { BoolProperty: true }) { }
```

### Nullable bool checks 4
[Via Mackinnon Buck](https://twitter.com/MackinnonBuck/status/1648856449592987650)

His opinion: *is long and accesses "Model" twice.*
```csharp
if (Model != null && Model.BoolProperty }) { }
```

### Nullable bool checks 5
[Via Brandon Minnick](https://twitter.com/TheCodeTraveler/status/1649072939088879618)

His opinion: *I find it has the best readability and requires the least amount of cognitive overhead to understand*
```csharp
if (Model?.BoolProperty is true) { }
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

## Patterns

> I think the thing makes people uncomfortable with pattern matching in C# is that it pushes variable declaration to the right instead of the left.

[Via David Fowler](https://twitter.com/davidfowl/status/1606503770992832513)

### Using less variables
[Via Nick Craver](https://twitter.com/Nick_Craver/status/1508068445644017667)

[For another example, I use it here](https://github.com/nikouu/pokesprite-spritesheet/blob/main/PokespriteGenerator/Decompressor.cs#L25)

Here we use pattern matching to get our variable at the same time as we `while`. And it compliles to the same thing as we would've traditionally done it.
```csharp
while(reader.ReadLine() is string line) 
{
	// work
}
```

### Parsing a kv string
[Via David Fowler](https://twitter.com/davidfowl/status/1497836113976721408)
```csharp
var dict = new Dictionary<string, string>();
foreach (var p in s.Split(',')
{
	if (p.Split('=') is [var k, var v])
	{
		dict[k] = v;
	}
}
```
Note:
- A KV parser improvement from the [switch expression example 7](https://github.com/nikouu/Switch-Expression-Examples/blob/main/README.md)

### Parsing a CSV
[Via David Fowler](https://twitter.com/davidfowl/status/1497840510735503364)
```csharp
var csv = "title,firstname,lastname\r\n.NET person, David, Fowler";
foreach (var line in csv.Split('\n'))
{
	if (line.Split(',') is [var title, var firstname, var lastName])
	{
		Console.WriteLine($"Title is {title}, Name is {firstname} {lastName}");
	}
}
```
Note:
- This is an improvement from the one above

## Proper Disposal 

### Synchronous 

[Via Nick Craver](https://twitter.com/Nick_Craver/status/1551578701564977153)

```csharp
foo?.Dispose();
```
Note:
- Nothing much to do when synchronous 

### Asynchronous

[Via Nick Craver](https://twitter.com/Nick_Craver/status/1551578701564977153)

```csharp
if (foo is IAsyncDisposable disposeMe)
{
    await disposeMe.DisposeAsync();
}
```

Note:
- This ensures you don't get a null reference exception on the dispose call

## Deconstruction

### Example 1

```csharp
foreach (var (key, value) in interestingDictionary))
{
}
```

## Empty Collections Initializers

### Array
[Documentation](https://learn.microsoft.com/en-us/dotnet/api/system.array.empty?view=net-7.0)
```csharp
var array = Array.Empty<T>();
```

### IEnumerable
[Docuemntation](https://learn.microsoft.com/en-us/dotnet/api/system.linq.enumerable.empty?view=net-7.0)
```csharp
var array = Enumerable.Empty<T>();
```

## Stackalloc patterns
## Create stack allocated span for under a certain length
[Via David Fowler](https://twitter.com/davidfowl/status/1703437753390924070)

```csharp
Span<object?> local = [null, null, null, null];
Span<object?> args = parameter.Length switch
{
    0 => [],
    <= 4 => local,
    _ => new object[parameter.Length]
}
```
### High performance byte/char manipulation 1
[Via David Fowler](https://twitter.com/davidfowl/status/1520966312817664000)

```csharp
// Choose a small stack threshold to avoid stack overflow
const int stackAllocThreshold = 256;

byte[]? pooled = null;

// Either use stack memory or pooled memory
Span<byte> span = bytesNeeded <= stackAllocThreshold
    ? stackalloc byte[stackAllocThreshold]
    : (pooled = ArrayPool<byte>.Shared.Rent(bytesNeeded);

// Write to the span and process it
var written = Populate(span);
Process(span[..written]);

// Return the pooled memory if we used it
if (pooled is not null)
{
    ArrayPool<byte>.Shared.Return(pooled);
}
```
Answer from [Miha Zupan](https://twitter.com/_MihaZupan/status/1521135981356896258) for why to use a flat 256 instead of the `bytesNeeded` for the stack allocation:
> If you use SkipLocalsInit (perf sensitive code like this should and does), a constant size stackalloc is more efficient. It also means you don't have to explicitly guard against passing a negative value to the stackalloc.

Answer to why 256 is used. You can also see [other places](https://grep.app/search?q=stackalloc&filter[lang][0]=C%23&filter[repo][0]=dotnet/runtime) it's used in .NET:
> It's conservative to avoid stack overflows.

### High performance byte/char manipulation 2
[A more in-depth version via David Fowler](https://twitter.com/davidfowl/status/1521008356864843777/photo/1)

```csharp
using var memory = bytesNeeded <= 256
    ? new StackOrPooledMemory(stackalloc byte[256])
    : new StackOrPooledMemory(bytesNeeded);

Span<byte> span = memory.Buffer;
var written = Populate(span);
Process(span[written..]);

ref struct StackOrPooledMemory
{
    private readonly Span<byte> _span;
    private byte[]? _arrayPooled;
    
    public StackOrPooledMemory(Span<byte> buffer)
    {
        _span = buffer;
        _arrayPooled = null;
    }

    public StackOrPooledMemory(int size)
    {
        var buffer = ArrayPool<byte>.Shared.Rent(size);
        _arrayPooled = buffer;
        _span = buffer;
    }

    public Span<byte> Buffer => _span;

    public void Dispose()
    {
        if (_arrayPooled is not null)
        {
            var pooled = _arrayPooled;
            _arrayPooled = null;
            ArrayPool<byte>.Shared.Return(pooled);
        }
    }
}
```
Answer from [David Fowler](https://twitter.com/davidfowl/status/1521011080373293056) as to why no try/finally:
> We often skip that in high performance code, if the buffer doesn't go back to the pool because of exceptions, it'll get GCed.
> 
And [another note](https://twitter.com/davidfowl/status/1521127114124058626):
> Try/finally prevents inlining

### String to GUID

Tries to make the conversion from string to GUID without allocation. However, according to the Twitter replies (see references below) there are more ways to improve this by:
- Moving off MD5
- Using UTF16
- Using `MemoryMarshal.AsBytes` instead of the UTF8 work

```csharp
Guid ComputeGuid(string stringToConvert)
{
    const int maxBytesOnStack = 256;

    var encoding = Encoding.UTF8;
    var maxByteCount = encoding.GetMaxByteCount(stringToConvert.Length);

    if (maxByteCount <= maxBytesOnStack)
    {
        var buffer = (Span<byte>)stackalloc byte[maxBytesOnStack];
        var written = encoding.GetBytes(stringToConvert, buffer);
        var utf8Bytes = buffer[..written];
        return HashData(utf8Bytes);
    }
    else
    {
        var utf8Bytes = encoding.GetBytes(stringToConvert);
        return HashData(utf8Bytes);
    }
}

Guid HashData(ReadOnlySpan<byte> bytes)
{
    var hashBytes = (Span<byte>)stackalloc byte[16];
    var written = MD5.HashData(bytes, hashBytes);

    return new Guid(hashBytes);
}
```

[Via Immo Landwerth (terrajobst)](https://twitter.com/terrajobst/status/1507808952146223106)

[GitHub repo it's used in](https://github.com/terrajobst/apisof.net/blob/31398940e1729982a7f5e56e0656beb55045c249/src/Terrajobst.UsageCrawling/ApiKey.cs#L11)

[My project with benchmarks against variations](https://github.com/nikouu/String-to-GUID-Dotnet-Benchmarking)

## WhenEach()

### Using cancellation with WhenEach()

[Via David Fowler](https://twitter.com/davidfowl/status/1778091262727577727)

```csharp
using var cts = new CalcellationTokenSource(timeout);
await foreach (var item in Task.WhenEach(connectionRequests).WithCancellation(cts.Token))
{
    if (!cts.TryReset())
    {
        cts.Token.ThrowIfCancellationRequested();
    }
    
    ProcessItem(item.Result);
    
    cts.CancelAfter(timeout);
}
```

## String.Split()

Splitting a string with no allocations. Requires you to know the amount of segments at compile time. 

[Via Sergiy Teplyakov](https://twitter.com/STeplyakov/status/1767748059256312258)

```csharp
var input = "1;2";

Span<Range> segments = stackalloc Range[2];
var source = input.AsSpan();
var segmentsCount = source.Split(segments, ';');

var a = int.Parse(source[segments[0]]);
var b = int.Parse(source[segments[1]]);
```

## Span<T>.Empty == null

While `Span<T>.Empty` feels like it shouldn't be a null due to a span being a struct which is a value type, Mark Gravell explains it well with:
>Span has a custom equality operator; that's what is being used here. The operator takes a span. It also has an implicit operator that takes T[]. What you're actually asking is: is Span<T>.Empty considered equal to the span created from a null T[] - which returns: Empty 

[Via Michael Rumpler](https://x.com/MRumpler/status/1642120446089854977)

[Via Mark Gravell](https://x.com/marcgravell/status/1642447224301912067)

## Never calls Dispose from a finalizer

[Via Kevin Gosse](https://x.com/KooKiz/status/1750463285965545725)

## Watch out for unbounded Task waiting

```csharp
async Task ProcessAsync(List<string> input)
{
	var tasks = new List<Task>();
	foreach(var item in input)
	{
		tasks.Add(ProcessItemAsync(item));
	}

	await Task.WhenAll(tasks);
}
```

We don't know the number of items in the incoming `List<T>` meaning we could spin up any number of tasks leading to CPU, IO, or memory problems. 

An alternative could be `Parallel.ForEachAsync<T>`, or any other practical concurrency control similar.

[Via Sergiy Teplyakov](https://x.com/STeplyakov/status/1800571705909481819)
