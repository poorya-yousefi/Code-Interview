# Memory Leak

Memory leak is a situation where a program allocates memory from the system but fails to release it back. This situation leads to memory exhaustion and the program may crash. Memory leaks are a common problem in programming, especially in languages like C and C++ where memory management is manual. In languages like Java and C#, memory management is automatic and the garbage collector takes care of releasing memory. However, they can also occur in managed environments because references are kept alive unnecessarily, preventing garbage collectors from reclaiming the memory.

## Common causes of memory leaks

- **Uncollected garbage**: In managed languages such as Java or C#, where GC is used, memory leaks often occur not because memory is not freed but because objects are unintentionally retained. This retention prevents the garbage collector from reclaiming the memory. Common culprits include `static fields`, `observers or event listeners` that are *not properly removed*(Not UnSubscribed), and `data structures` that *grow indefinitely*.
- **Improper resource management**: In managed and unmanaged environments, failing to release resources such as `file handles`, `database connections`, or `network sockets` can lead to memory leaks.
- **Circular references**: Circular references can lead to memory leaks, particularly in languages with automatic GC. This situation occurs when two or more objects reference each other, creating a cycle that can prevent the garbage collector from determining that they are no longer in use and should thus be collected.
- **Global variables**: Overuse of global variables or extensive caching mechanisms without proper eviction policies can cause memory to be consumed progressively over time. Global variables remain in memory for the *lifetime* of an application(`Singleton`). If they reference large data structures or objects, they can significantly contribute to memory leaks.
- **Closures**: In languages that support closures (functions that can capture and retain state from their surrounding scope), keeping more objects in memory is easier than intended. If a closure captures a large object or a complex scope but only uses a small part of it, the entire captured scope remains in memory, which can lead to a memory leak if it prevents the GC from cleaning up the reference.
- **Third-party code and libraries**: Dependencies on third-party libraries or frameworks can also introduce memory leaks. If these libraries do not correctly manage memory or resources, or if they retain more objects in memory than necessary, they can cause leaks in applications that use them. Regular updates and careful selection of well-maintained libraries can mitigate this risk.
- **Improper in-memory caching**: In-memory caching can significantly improve the performance of an application by reducing the need for repetitive data fetching and processing. However, if not managed properly, it can also lead to memory leaks under the following conditions:
  - A *size limit* and *expiration policy* are not configured. The cache may grow without bounds and eventually consume all available memory.
  - Cached items are no longer needed but are not explicitly removed.
  - A cache retains a strong reference to an entry, preventing GC even if it is no longer used elsewhere in the application.

## Mitigate memory leaks in .NET applications

### Code Review

Some coding patterns that are well-known for their potential to create memory leaks include, but are not limited to, the following:

- **Unclosed resources** (such as files or network connections): It is essential to ensure that the resources consumed for these connections are freed as soon as the operation is completed.
- **Undetached event listeners**: In .NET, it’s easy to create memory leaks through event handlers by subscribing to events and failing to unsubscribe. Reviewers should ensure that event handlers are properly detached, especially for long-lived publishers.
- **Misuse of static variables**: Static fields or properties referencing large objects or collections can keep those objects alive indefinitely. Reviewers can look for unnecessary static references that could be converted to instance-level or more transient scopes.

#### Static variable problem example

```csharp
namespace API.Chapter04.Controllers;

[ApiController]
[Route("[controller]")]
public class WeatherForecastController(IWeatherService weatherService) : ControllerBase
{
private static List<string> _leakyList = new List<string>();

    [HttpGet]
    public IEnumerable<string> Get()
    {
        // Simulate memory leak
        _leakyList.Add(new string('a', 1024 * 1024));
        // Add 1MB to the list
        return new string[] { "Data added to the leaky list.Total items: " + _leakyList.Count };
    }
}
```

> This code introduces a memory leak by adding a large string (1 MB) to a static list each time the Get method is called. The list keeps growing with no mechanism to clear or remove the items, simulating a memory leak.

While conducting a code review, always seek to identify static fields and properties that might hold references to large objects or object graphs. If a static reference is found, question its necessity and suggest alternatives such as `dependency injection` (DI) or `instance-based management` where appropriate.

#### Address this problems

- **Use a local variable**: Remove the `static` list and use local variables or instance-level storage as needed, ensuring that they are collected by GC when they are no longer in use.
- **Limit the list size**: If the `static` variable serves a functional purpose, decide on a reasonable list size limit. Once that limit is reached, you can start removing old items before adding new ones or clearing the list periodically.
- **Use a more optimized data structure**: If the `static` data must be preserved, consider using more memory-efficient data structures or techniques that better suit your requirements, such as a `fixed-size queue`.

#### Fix static variable problem

> After refactoring this method to use a scoped list variable instead of a static one, the object is *destroyed after each request cycle*. The resulting endpoint looks like this:

```csharp
namespace API.Chapter04.Controllers;

[ApiController]
[Route("[controller]")]
public class WeatherForecastController(IWeatherService weatherService) : ControllerBase
{
    private List<string> _properList = new List<string>();

    [HttpGet]
    [Route("GetRefactored")]
    public IEnumerable<string> GetRefactored()
    {
        // Simulate memory leak
        _properList.Add(new string('a', 1024 * 1024));
        // Add 1MB to the list
        return new string[] { "Data added to the proper list.Total items: " + _properList.Count };
    }
}
```

### Stress Testing

Stress testing is a powerful development technique. It can uncover memory leaks that might not be evident during standard testing or at lower application usage levels. This approach is crucial for identifying memory leaks, as these often accumulate over time or under conditions of intense resource demand.
**Monitoring memory** usage under stress can highlight unusual patterns or unexpected growth in memory consumption.

> Memory leaks in .NET applications might not always be immediately apparent, especially if they are slow and accumulate over time.

A gradual increase in memory usage might go unnoticed during regular testing or initial deployment phases. Real-world applications often face variable loads, with peak times significantly stressing system resources. Stress testing mimics these scenarios, providing insights into how the application manages memory under heavy loads.

> Stress testing can reveal scenarios wherein the garbage collector cannot reclaim memory efficiently, possibly due to large object heap fragmentation, excessive pinning of memory, or improper disposal patterns.

Some best practices that should govern your stress testing activities include the following:

- Use realistic load scenarios: Ensure that the stress tests mimic realistic user behaviors and load patterns as closely as possible
- Monitor and measure: Utilize .NET performance counters, memory profilers, and logging to monitor memory usage and system behavior under stress
- Analyze GC metrics: Pay attention to GC performance metrics to understand how effectively memory is being managed and reclaimed
- Iterate and refine: Stress testing should be an iterative process, whereby findings from each test are used to refine the application and improve its resilience and memory management.

> We will use Postman, a powerful HTTP, HTTPS, and SOAP/REST client and testing tool,
to perform stress testing on web applications. (choose Run collection and Performance tab.)

## References

- Trevoir Williams (2024). Effective .NET Memory Management. Packt Publishing Ltd.
