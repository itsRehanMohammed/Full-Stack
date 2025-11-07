# .NET & ASP.NET Core Interview Guide

## Table of Contents

### [Fundamental Concepts](#fundamental-concepts)
- [OOPS (Object-Oriented Programming)](#oops-object-oriented-programming)
- [.NET Core vs .NET](#net-core-vs-net)

### [ASP.NET Core MVC](#aspnet-core-mvc)
- [ViewBag/ViewData](#viewbagviewdata)
- [ActionFilters & AttributeFilters](#actionfilters--attributefilters)
- [Controller vs ApiController](#controller-vs-apicontroller)
- [ActionResult vs IActionResult](#actionresult-vs-iactionresult)
- [Dependency Injection Scopes](#dependency-injection-scopes)
- [Authentication vs Authorization](#authentication-vs-authorization)
- [Middlewares](#middlewares)
- [Global Exception Handling](#global-exception-handling)
- [Model Binding & Validation](#model-binding--validation)
- [API Versioning](#api-versioning)
- [CORS (Cross-Origin Resource Sharing)](#cors-cross-origin-resource-sharing)

### [Design Patterns & Architecture](#design-patterns--architecture)
- [Repository and Unit of Work Patterns](#repository-and-unit-of-work-patterns)
- [Asynchronous vs Synchronous Programming](#asynchronous-vs-synchronous-programming)
- [Caching in Web API](#caching-in-web-api)
- [Securing Web API with HTTPS](#securing-web-api-with-https)
- [File Uploads & Large Data](#file-uploads--large-data)
- [Swagger/Swashbuckle](#swaggerswashbuckle)

### [Entity Framework Core](#entity-framework-core)
- [EF Core vs EF 6](#ef-core-vs-ef-6)
- [DBContext](#dbcontext)
- [Configuring Relationships](#configuring-relationships)
- [Migrations](#migrations)
- [Loading Strategies](#loading-strategies)
- [ConcurrencyCheck Attribute](#concurrencycheck-attribute)
- [Soft Delete](#soft-delete)
- [Pagination with .Skip and .Take](#pagination-with-skip-and-take)
- [Connection Resiliency](#connection-resiliency)

### [Advanced .NET Core Concepts](#advanced-net-core-concepts)
- [Kestrel vs IIS](#kestrel-vs-iis)
- [Startup Class (.NET 5/6)](#startup-class-net-56)
- [Tag Helpers vs HTML Helpers](#tag-helpers-vs-html-helpers)
- [Razor Pages vs MVC](#razor-pages-vs-mvc)
- [Options Pattern](#options-pattern)
- [Logging](#logging)
- [Health Checks](#health-checks)
- [AddMvc vs AddMvcCore](#addmvc-vs-addmvccore)
- [SignalR](#signalr)
- [Configuration](#configuration)
- [.NET Standard vs .NET 5/6/7/8](#net-standard-vs-net-5678)
- [IHttpClientFactory](#ihttpclientfactory)
- [Hosted Services (Background Services)](#hosted-services-background-services)
- [Task.Run() vs await](#taskrun-vs-await)
- [Minimal APIs (.NET 6+)](#minimal-apis-net-6)
- [JWT Authentication](#jwt-authentication)
- [gRPC vs REST](#grpc-vs-rest)
- [Collection Interfaces](#collection-interfaces)
- [Deployment Models](#deployment-models)

### [LINQ](#linq)
- [Syntax Styles](#syntax-styles)

---

## Fundamental Concepts

### OOPS (Object-Oriented Programming)
It's a way of designing software using "objects" which contain both data and methods. The main ideas are:

- **Encapsulation:** Bundling data and methods together, hiding internal details
- **Inheritance:** A class can inherit from another class (code reuse)
- **Polymorphism:** Same action can be performed in different ways
- **Abstraction:** Hiding complex details, showing only essentials

### .NET Core vs .NET
- **.NET Core:** Open-source, cross-platform successor to .NET Framework
- **.NET (5+):** Unified platform merging .NET Core, .NET Framework, and Xamarin

---

## ASP.NET Core MVC

### ViewBag/ViewData
- Pass data from **Controller → View**
- **ViewBag:** Dynamic property (`ViewBag.MyData = "Hello"`)
- **ViewData:** Dictionary object (`ViewData["MyData"] = "Hello"`)
- Same data, different access methods

### ActionFilters & AttributeFilters
- "Middleware" for controller actions
- Run code **before/after** action executes
- **AttributeFilter:** Applied as attribute like `[Authorize]`

### Controller vs ApiController
- **Controller (MVC):** Returns Views (HTML)
- **ApiController (Web API):** Returns data (JSON/XML)

### ActionResult vs IActionResult
- `IActionResult`: Interface - more flexible
- `ActionResult`: Class implementing `IActionResult`
- Use `IActionResult` when returning different result types

### Dependency Injection Scopes
- **Transient:** New instance every time requested
- **Scoped:** One instance per web request (default for DbContext)
- **Singleton:** One instance for application lifetime

### Authentication vs Authorization
- **Authentication:** "Who are you?" (Login)
- **Authorization:** "What can you do?" (Permissions)

### Middlewares
Software components in application pipeline:
- Handle requests/responses
- Examples: Authentication, Static Files, Routing
- Each middleware can process request and pass to next

### Global Exception Handling
- Use `UseExceptionHandler` middleware
- Custom Exception Filters for MVC actions
- Prevents application crashes

### Model Binding & Validation
- **Model Binding:** Maps HTTP request data to action parameters
- **Model Validation:** Checks data against rules (`[Required]`, `[StringLength]`)
- Check with `ModelState.IsValid`

### API Versioning
Manage API changes without breaking clients:
- URL versioning: `/api/v1/products`
- Query string: `/api/products?api-version=1.0`
- HTTP headers

### CORS (Cross-Origin Resource Sharing)
Security mechanism allowing web pages from one domain to access resources from another domain
- Must explicitly enable with `AddCors` middleware

---

## Design Patterns & Architecture

### Repository and Unit of Work Patterns
- **Repository Pattern:** Abstraction layer between business logic and data access
- **Unit of Work Pattern:** Tracks all changes and commits as single transaction
- In EF Core, `DbContext` acts as Unit of Work

### Asynchronous vs Synchronous Programming
- **Synchronous:** Thread blocked until task completes
- **Asynchronous:** Thread freed while waiting for task
- Use `async` and `await` for scalability

### Caching in Web API
- **In-Memory Caching:** Fast, server-specific (not shared)
- **Distributed Caching:** Shared cache (Redis, SQL Server) for multi-server environments

### Securing Web API with HTTPS
- Encrypts client-server communication
- Use SSL/TLS certificates
- `[RequireHttps]` attribute

### File Uploads & Large Data
- **Uploads:** Use `IFormFile` and stream to storage
- **Large Downloads:** Use streaming instead of loading full file into memory

### Swagger/Swashbuckle
- Automatic interactive API documentation
- Test endpoints directly from browser
- OpenAPI standard

---

## Entity Framework Core

### EF Core vs EF 6
- **EF Core:** Modern, cross-platform, lightweight
- **EF 6:** Mature, Windows-only (.NET Framework)

### DBContext
Heart of EF Core:
- Query data
- Track changes
- Save changes
- Registered as **Scoped** service

### Configuring Relationships
Define entity relationships in `OnModelCreating`:
```csharp
// One-to-Many
modelBuilder.Entity<Blog>()
    .HasMany(b => b.Posts)
    .WithOne(p => p.Blog);
```

### Migrations
Keep database schema in sync with entity models:
- `Add-Migration` - creates migration
- `Update-Database` - applies migration

### Loading Strategies
- **Eager Loading:** Load main + related entities with `.Include()`
- **Lazy Loading:** Load related entities when accessed (requires `virtual`)
- **Explicit Loading:** Manually load related data later

### ConcurrencyCheck Attribute
- Enables optimistic concurrency control
- Prevents "last write wins" scenarios
- Throws `DbUpdateConcurrencyException` on conflicts

### Soft Delete
- Mark records as deleted instead of actual deletion
- Use `IsDeleted` column + global query filter
```csharp
modelBuilder.Entity<Blog>().HasQueryFilter(b => !b.IsDeleted);
```

### Pagination with .Skip and .Take
```csharp
var pagedData = context.Products
    .Skip((pageNumber - 1) * pageSize)
    .Take(pageSize)
    .ToList();
```

### Connection Resiliency
Handle temporary database failures:
- Automatic retry policies
- `.UseSqlServer(..., options => options.EnableRetryOnFailure())`

---

## Advanced .NET Core Concepts

### Kestrel vs IIS
- **Kestrel:** Cross-platform, built-in web server
- **IIS:** Windows web server
- **Production:** Kestrel behind reverse proxy (IIS, Nginx)

### Startup Class (.NET 5/6)
- **ConfigureServices:** Register DI dependencies
- **Configure:** Setup request pipeline (middleware)
- **.NET 6+:** Done in `Program.cs` (minimal hosting model)

### Tag Helpers vs HTML Helpers
- **HTML Helper:** `@Html.LabelFor(m => m.Email)`
- **Tag Helper:** `<label asp-for="Email"></label>`
- Tag Helpers look like standard HTML

### Razor Pages vs MVC
- **Razor Pages:** Page-centric scenarios (contact forms, simple pages)
- **MVC:** Complex applications with multiple views, APIs

### Options Pattern
Strongly-typed configuration access:
- **`IOptions<T>`:** Singleton, doesn't detect changes
- **`IOptionsSnapshot<T>`:** Scoped, detects changes per request
- **`IOptionsMonitor<T>`:** Singleton that can detect changes

### Logging
Built-in API for application messages:
- **Levels:** Trace < Debug < Information < Warning < Error < Critical
- **Providers:** Console, Debug, File, Azure App Insights
- Inject `ILogger<T>`

### Health Checks
Endpoints to check application health:
- `/health` endpoint
- Database connectivity checks
- `AddHealthChecks()` and `MapHealthChecks()`

### AddMvc vs AddMvcCore
- **AddMvcCore():** Minimal MVC setup
- **AddMvc():** Includes all standard features + AddMvcCore

### SignalR
Real-time web functionality:
- Server can push content to clients
- Live chats, dashboards, notifications

### Configuration
Unified system from multiple sources:
- `appsettings.json`
- Environment variables
- Command-line arguments
- User secrets

### .NET Standard vs .NET 5/6/7/8
- **.NET Standard:** API specification (contract)
- **.NET 5/6/7/8:** Actual implementations (runtimes)

### IHttpClientFactory
Factory for `HttpClient` instances:
- Manages socket exhaustion
- Connection pooling
- Centralized configuration

### Hosted Services (Background Services)
Long-running background tasks:
- Queue processing
- Scheduled emails
- Data cleanup

### Task.Run() vs await
- **`await`:** For I/O-bound work (async waiting)
- **`Task.Run()`:** For CPU-bound work (push to background thread)

### Minimal APIs (.NET 6+)
- `Startup.cs` merged into `Program.cs`
- Less boilerplate code
- Same functionality

### JWT Authentication
1. User logs in → server creates signed JWT
2. Client stores JWT
3. Send JWT in `Authorization: Bearer <token>` header
4. Server validates token

### gRPC vs REST
- **gRPC:** High-performance, HTTP/2, binary (internal microservices)
- **REST:** Widely understood, JSON (public APIs)

### Collection Interfaces
- **`IEnumerable<T>`:** Basic iteration only
- **`ICollection<T>`:** + Add/Remove/Count
- **`IList<T>`:** + Indexing

### Deployment Models
- **Framework-dependent:** Requires .NET runtime on server
- **Self-contained:** Includes .NET runtime (larger package)

---

## LINQ

### Syntax Styles
1. **Query Syntax** (like SQL):
   ```csharp
   var result = from p in products
                where p.Price > 10
                select p.Name;
   ```

2. **Method Syntax** (lambda expressions):
   ```csharp
   var result = products.Where(p => p.Price > 10)
                       .Select(p => p.Name);
   ```

- **With EF Core:** Translated to SQL, runs on database
- **With Collections:** Runs in application memory