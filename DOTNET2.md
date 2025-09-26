# .NET Core Interview Preparation Guide

## Table of Contents

1. [Dependency Injection](#dependency-injection)
2. [Middlewares](#middlewares)
3. [Swagger](#swagger)
4. [Service Lifetimes](#service-lifetimes)
5. [Kestrel](#kestrel)
6. [Async/Await](#asyncawait)
7. [JWT](#jwt)
8. [API Consumption](#api-consumption)
9. [CORS](#cors)
10. [HTTP Methods](#http-methods)
11. [MVC Routing](#mvc-routing)
12. [Partial Views](#partial-views)
13. [Code First vs Database First](#code-first-vs-database-first)
14. [MVC Filters](#mvc-filters)
15. [ViewBag vs ViewData](#viewbag-vs-viewdata)
16. [Bundling](#bundling)
17. [jQuery](#jquery)
18. [Composite Primary Key](#composite-primary-key)
19. [UDT](#udt)
20. [CTE & Temp Tables](#cte--temp-tables)
21. [Dictionary vs Hashtable](#dictionary-vs-hashtable)
22. [readonly vs const](#readonly-vs-const)
23. [IQueryable vs IEnumerable](#iqueryable-vs-ienumerable)
24. [Virtual Class](#virtual-class)
25. [Sealed Class](#sealed-class)
26. [SOLID Principles](#solid-principles)
27. [Source Code Management](#source-code-management)

---

## Dependency Injection

**Dependency Injection (DI)** is a design pattern that implements Inversion of Control (IoC) where objects receive their dependencies from an external source rather than creating them internally.

**.NET Core DI** has built-in support for dependency injection through `IServiceCollection`.

```csharp
// Registration
services.AddScoped<IUserService, UserService>();
services.AddSingleton<ILogger, Logger>();
services.AddTransient<IEmailService, EmailService>();

// Consumption
public class UserController : Controller
{
    private readonly IUserService _userService;

    public UserController(IUserService userService) // Constructor injection
    {
        _userService = userService;
    }
}
```

## Middlewares

**Middleware** are components that handle requests and responses in the ASP.NET Core pipeline. They are executed in the order they are registered.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseMiddleware<CustomMiddleware>();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseEndpoints(endpoints => endpoints.MapControllers());
}
```

**Common Middlewares:**

- `UseRouting()`: Endpoint routing
- `UseAuthentication()`: Authentication
- `UseAuthorization()`: Authorization
- `UseStaticFiles()`: Serve static files
- `UseCors()`: CORS handling

## Swagger

**Swagger/OpenAPI** is a specification for documenting RESTful APIs. In .NET Core, we use **Swashbuckle**:

```csharp
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
});

app.UseSwagger();
app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API v1"));
```

## Service Lifetimes

### Scoped

- **One instance per request**
- Created once per HTTP request
- Ideal for Entity Framework DbContext

### Transient

- **New instance every time**
- Created each time they're requested
- Lightweight, stateless services

### Singleton

- **Single instance for application lifetime**
- Created first time requested
- Shared across all requests

```csharp
services.AddScoped<IUserService, UserService>();      // Per request
services.AddTransient<IEmailService, EmailService>(); // Always new
services.AddSingleton<ICacheService, CacheService>(); // Single instance
```

## Kestrel

**Kestrel** is a cross-platform, lightweight web server for ASP.NET Core. It's the default server and can run independently or behind a reverse proxy (IIS, Nginx).

**Features:**

- Cross-platform (Windows, Linux, macOS)
- High performance
- HTTPS support
- HTTP/2 support

## Async/Await

**Async/Await** enables asynchronous programming, freeing up threads while waiting for I/O operations.

```csharp
public async Task<IActionResult> GetUsers()
{
    var users = await _userService.GetUsersAsync();
    return Ok(users);
}

public class UserService
{
    public async Task<List<User>> GetUsersAsync()
    {
        return await _context.Users.ToListAsync();
    }
}
```

**Key Points:**

- `async` method should return `Task`, `Task<T>`, or `void` (avoid void)
- `await` releases the thread while waiting
- Prevents thread pool exhaustion

## JWT (JSON Web Token)

**JWT** is a compact, URL-safe token format for secure information exchange.

**Structure:**

```
Header.Payload.Signature
```

**Implementation:**

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = "your-issuer",
            ValidAudience = "your-audience",
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("your-secret-key"))
        };
    });
```

## API Consumption

**Consuming APIs** in .NET Core using `HttpClient`:

```csharp
public class ApiService
{
    private readonly HttpClient _httpClient;

    public ApiService(HttpClient httpClient)
    {
        _httpClient = httpClient;
    }

    public async Task<User> GetUserAsync(int id)
    {
        var response = await _httpClient.GetAsync($"/api/users/{id}");
        response.EnsureSuccessStatusCode();
        return await response.Content.ReadAsAsync<User>();
    }
}

// Registration
services.AddHttpClient<ApiService>();
```

## CORS (Cross-Origin Resource Sharing)

**CORS** allows web applications to request resources from different domains.

**Configuration:**

```csharp
services.AddCors(options =>
{
    options.AddPolicy("AllowSpecificOrigin",
        builder => builder.WithOrigins("https://example.com")
                          .AllowAnyHeader()
                          .AllowAnyMethod());
});

app.UseCors("AllowSpecificOrigin");
```

## HTTP Methods

| Method | Description             | Idempotent |
| ------ | ----------------------- | ---------- |
| GET    | Retrieve resource       | Yes        |
| POST   | Create resource         | No         |
| PUT    | Update/replace resource | Yes        |
| PATCH  | Partial update          | No         |
| DELETE | Remove resource         | Yes        |
| HEAD   | Get headers only        | Yes        |

## MVC Routing

**Routing** maps URLs to controller actions.

**Convention Routing:**

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
});
```

**Attribute Routing:**

```csharp
[Route("api/[controller]")]
[ApiController]
public class UsersController : ControllerBase
{
    [HttpGet("{id}")]
    public IActionResult GetUser(int id) { }

    [HttpPost]
    public IActionResult CreateUser(User user) { }
}
```

## Partial Views

**Partial Views** are reusable view components.

**Creating:**

```html
<!-- _UserDetails.cshtml -->
<div class="user-info">
  <h3>@Model.Name</h3>
  <p>@Model.Email</p>
</div>
```

**Using:**

```html
@Html.Partial("_UserDetails", Model.User)

<!-- or -->
<partial name="_UserDetails" model="Model.User" />
```

## Code First vs Database First

### Code First

- Define classes first → Database generated automatically
- Full control over model classes
- Better for Domain-Driven Design
- Migrations track schema changes

### Database First

- Database exists first → Classes generated automatically
- Good for existing databases
- Less control over model classes

## MVC Filters

**Filters** execute code at specific points in the request pipeline.

**Types:**

- **Authorization**: `IAuthorizationFilter`
- **Action**: `IActionFilter`
- **Result**: `IResultFilter`
- **Exception**: `IExceptionFilter`
- **Resource**: `IResourceFilter`

```csharp
public class CustomActionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        // Before action execution
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        // After action execution
    }
}

// Usage
[ServiceFilter(typeof(CustomActionFilter))]
public class HomeController : Controller { }
```

## ViewBag vs ViewData

### ViewBag

- Dynamic property (`ViewBag.Message = "Hello"`)
- No type casting needed
- Runtime resolution

### ViewData

- Dictionary (`ViewData["Message"] = "Hello"`)
- Requires type casting
- Better performance

```csharp
ViewBag.UserName = "John";
ViewData["UserName"] = "John";

// In View
@ViewBag.UserName
@(ViewData["UserName"] as string)
```

## Bundling

**Bundling** combines multiple files into single files to reduce HTTP requests.

```csharp
// BundleConfig.cs
public class BundleConfig
{
    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));

        bundles.Add(new StyleBundle("~/Content/css").Include(
                    "~/Content/site.css"));
    }
}

// In View
@Scripts.Render("~/bundles/jquery")
@Styles.Render("~/Content/css")
```

## jQuery

**jQuery** is a JavaScript library simplifying DOM manipulation and AJAX.

**Common Usage:**

```javascript
$(document).ready(function () {
  // DOM manipulation
  $("#submitBtn").click(function () {
    $.ajax({
      url: "/api/users",
      type: "POST",
      data: $("#userForm").serialize(),
      success: function (response) {
        $("#result").html("User created successfully");
      },
    });
  });
});
```

## Composite Primary Key

A **composite primary key** uses multiple columns to uniquely identify a row.

```sql
CREATE TABLE OrderItems (
    OrderID INT,
    ProductID INT,
    Quantity INT,
    PRIMARY KEY (OrderID, ProductID)
);
```

**Entity Framework:**

```csharp
modelBuilder.Entity<OrderItem>()
    .HasKey(oi => new { oi.OrderID, oi.ProductID });
```

## UDT (User-Defined Type)

**UDT** allows creating custom data types in SQL Server.

```sql
CREATE TYPE AddressType AS TABLE (
    Street NVARCHAR(100),
    City NVARCHAR(50),
    ZipCode NVARCHAR(10)
);

-- Usage in stored procedure
CREATE PROCEDURE InsertCustomer
    @Addresses AddressType READONLY
AS
BEGIN
    INSERT INTO CustomerAddresses SELECT * FROM @Addresses
END
```

## CTE & Temp Tables

### CTE (Common Table Expression)

- Temporary result set within a query
- Not stored in database
- Better readability

```sql
WITH SalesCTE AS (
    SELECT ProductID, SUM(Quantity) as TotalSold
    FROM OrderDetails
    GROUP BY ProductID
)
SELECT p.Name, s.TotalSold
FROM Products p
JOIN SalesCTE s ON p.ProductID = s.ProductID;
```

### Temp Tables

- Temporary storage in tempdb
- Persist for session/scope
- Better for complex operations

```sql
SELECT * INTO #TempProducts FROM Products WHERE Price > 100;
SELECT * FROM #TempProducts;
DROP TABLE #TempProducts;
```

## Dictionary vs Hashtable

### Dictionary<TKey, TValue>

- Generic type
- Type-safe
- Better performance
- No boxing/unboxing

### Hashtable

- Non-generic
- Stores objects
- Boxing/unboxing overhead
- Thread-safe for multiple readers

```csharp
Dictionary<string, int> dict = new Dictionary<string, int>();
dict.Add("one", 1);

Hashtable ht = new Hashtable();
ht.Add("one", 1);
```

## readonly vs const

### const

- Compile-time constant
- Must be initialized at declaration
- Implicitly static

### readonly

- Runtime constant
- Can be initialized in constructor
- Instance-level or static

```csharp
public class Constants
{
    public const double PI = 3.14159;                    // Compile-time
    public readonly string ConnectionString;            // Runtime

    public Constants(string connString)
    {
        ConnectionString = connString;                  // Can set in constructor
    }
}
```

## IQueryable vs IEnumerable

### IEnumerable

- In-memory collection
- LINQ-to-Objects
- Client-side filtering

### IQueryable

- Database query
- LINQ-to-SQL/Entities
- Server-side filtering

```csharp
// IEnumerable - brings all records to client
IEnumerable<User> users = context.Users.Where(u => u.Age > 25).ToList();

// IQueryable - executes filter on database
IQueryable<User> users = context.Users.Where(u => u.Age > 25);
```

## Virtual Class

**Virtual methods** can be overridden in derived classes.

```csharp
public class Animal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("Animal sound");
    }
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Bark");
    }
}
```

## Sealed Class

**Sealed classes** cannot be inherited.

```csharp
public sealed class Logger
{
    public void Log(string message) { }
}

// This will cause error:
// public class FileLogger : Logger { }
```

## SOLID Principles

**S** - Single Responsibility Principle  
_A class should have only one reason to change_

**O** - Open/Closed Principle  
_Open for extension, closed for modification_

**L** - Liskov Substitution Principle  
_Subtypes should be substitutable for their base types_

**I** - Interface Segregation Principle  
_Many specific interfaces are better than one general interface_

**D** - Dependency Inversion Principle  
_Depend on abstractions, not concretions_

## Source Code Management

### Git

- Distributed version control
- Branching and merging
- Staging area
- Popular platforms: GitHub, GitLab, Azure DevOps

### TFS (Team Foundation Server)

- Centralized version control (TFVC)
- Integrated with Azure DevOps
- Work item tracking
- Build automation

**Common Git Commands:**

```bash
git clone <repository>
git add <files>
git commit -m "message"
git push origin master
git pull origin master
git branch <branch-name>
git merge <branch-name>
```

---

## Topic Grade Assessment

### .NET Core: ⭐⭐⭐⭐⭐ (Expert)

- Dependency Injection, Middleware, Kestrel, Configuration

### Web API/.NET Core API: ⭐⭐⭐⭐⭐ (Expert)

- REST principles, Controllers, Routing, Swagger, JWT

### ASP.NET MVC: ⭐⭐⭐⭐ (Advanced)

- Routing, Filters, Views, Model Binding

### SQL: ⭐⭐⭐⭐ (Advanced)

- CTE, Temp Tables, UDT, Composite Keys

### C#: ⭐⭐⭐⭐⭐ (Expert)

- Async/Await, Collections, OOP Principles

### Advanced Concepts: ⭐⭐⭐⭐ (Advanced)

- SOLID, Design Patterns, Performance

### Source Code Management: ⭐⭐⭐ (Intermediate)

- Git/TFS basic to intermediate usage

---

**Interview Tips:**

- Practice explaining concepts in simple terms
- Prepare real-world examples
- Understand when to use each technology
- Be ready for coding exercises
- Review your past projects

Good luck with your interview! 🚀
