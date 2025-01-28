# Caching

Caching is a vital optimization technique in application development.It helps improve performance and scalability by reducing the needs for repetitive computation or data retrieval.

## Introduction to Caching

Caching involves temporarily storing data in a fast-access storage layer to improve response times and reduce system loading. In .NET, you can use built-in caching mechanisms to implement various caching strategies:

- **In-Memory Caching**: Ideal for single-server applications.
- **Distributed Caching**: Suitable for multi-server environments.
- **Hybrid Caching**: Combines the benefits of in-memory and distributed caching.

### In-Memory Caching

In-Memory Caching stores data in the server’s RAM, making it fast. However, it is limited to a single server and is unsuitable for distributed systems where multiple servers are involved.

```csharp
using Microsoft.Extensions.Caching.Memory;

public class CacheManager<T>
{
    private readonly IMemoryCache _memoryCache;

    public CacheManager(IMemoryCache memoryCache)
    {
        _memoryCache = memoryCache;
    }

    public T GetOrSet(string key, Func<T> fetchFunction, TimeSpan cacheDuration)
    {
        if (!_memoryCache.TryGetValue(key, out T value))
        {
            value = fetchFunction();
            _memoryCache.Set(key, value, cacheDuration);
        }

        return value;
    }
}
```

### Distributed Caching

Distributed Caching stores data in a centralized external system (e.g., Redis, SQL Server),allowing all servers in a distributed system to share the same cache. This makes it suitable for multi-server environments.

```csharp
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379"; // Replace with your Redis configuration
});

using Microsoft.Extensions.Caching.Distributed;
using System.Text.Json;

public class DistributedCacheManager<T>
{
    private readonly IDistributedCache _distributedCache;

    public DistributedCacheManager(IDistributedCache distributedCache)
    {
        _distributedCache = distributedCache;
    }

    public async Task<T> GetOrSetAsync(string key, Func<Task<T>> fetchFunction, TimeSpan cacheDuration)
    {
        var cachedData = await _distributedCache.GetStringAsync(key);

        if (cachedData != null)
        {
            return JsonSerializer.Deserialize<T>(cachedData)!;
        }

        var data = await fetchFunction();
        var serializedData = JsonSerializer.Serialize(data);

        await _distributedCache.SetStringAsync(
            key,
            serializedData,
            new DistributedCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = cacheDuration
            });

        return data;
    }
}
```

### Hybrid Caching

Hybrid Caching combines the speed of in-memory caching with the scalability of distributed caching. Data is first checked in-memory and, if unavailable, fetched from the distributed cache.

```csharp
public class HybridCacheManager<T>
{
    private HybridCache _cache;
    public HybridCacheManager(HybridCache cache)
    {
        _cache = cache;
    }
    public async Task<string> GetCachedDataAsync(string key, CancellationToken token = default)
    {
        return await _cache.GetOrCreateAsync(
            $"{key}",
            async cancel => await GetDataAsync(key, cancel),
            token: token
        );
    }
    private async Task<string> GetDataAsync(string key, CancellationToken token)
    {
        return $"Data for cache entry with key: {key}";
    }
}
```

## Related

- [FusionCache](https://github.com/ZiggyCreatures/FusionCache)
