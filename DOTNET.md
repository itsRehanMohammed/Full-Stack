# .NET Core Interview Preparation Guide

## 🎯 **Dependency Injection (DI) in .NET Core**

### What is Dependency Injection?

DI is a design pattern where objects receive their dependencies from an external source rather than creating them internally.

### Real-life Example:

Think of a restaurant:

- **Without DI**: Each chef grows their own vegetables, raises animals → inefficient
- **With DI**: A supplier provides all ingredients → chefs focus on cooking

### In .NET Core:

```csharp
// Service Registration
services.AddScoped<IEmailService, EmailService>();

// Constructor Injection
public class UserController
{
    private readonly IEmailService _emailService;
    public UserController(IEmailService emailService)
    {
        _emailService = emailService;
    }
}
```

### Service Lifetimes:

- **Transient**: New instance every time (like taking a taxi)
- **Scoped**: One instance per request (like a hotel room for your stay)
- **Singleton**: Single instance for entire application (like a shared company car)

---

## 🌉 **Middlewares in .NET Core**

### What are Middlewares?

Software components that handle requests and responses in a pipeline.

### Real-life Example:

Airport security check:

- Baggage scan → Passport check → Boarding pass verification → Gate entry

### In .NET Core:

```csharp
app.UseAuthentication();    // Check identity
app.UseAuthorization();     // Check permissions
app.UseRouting();          // Route to correct endpoint
app.UseEndpoints();        // Execute the endpoint
```

---

## 📚 **Swagger in .NET Core**

### What is Swagger?

Automated API documentation tool.

### Real-life Example:

Like a restaurant menu that shows all dishes, ingredients, and prices.

### Implementation:

```csharp
services.AddSwaggerGen();
app.UseSwagger();
app.UseSwaggerUI();
```

---

## ⚡ **Async and Await**

### What is it?

A way to write non-blocking code.

### Real-life Example:

- **Synchronous**: Waiting in line at coffee shop (can't do anything else)
- **Asynchronous**: Order coffee, get a token, browse phone while waiting

### Code Example:

```csharp
public async Task<List<User>> GetUsersAsync()
{
    return await _context.Users.ToListAsync();
}
```

---

## 🔐 **JWT (JSON Web Tokens)**

### What is JWT?

A compact way to securely transmit information between parties as a JSON object.

### Structure:

```
Header.Payload.Signature
```

### Real-life Example:

Like a concert wristband that proves you paid and can access certain areas.

### Implementation:

```csharp
var token = new JwtSecurityToken(
    issuer: "your-issuer",
    audience: "your-audience",
    claims: claims,
    expires: DateTime.Now.AddHours(1),
    signingCredentials: credentials
);
```

---

## 🌐 **CORS (Cross-Origin Resource Sharing)**

### What is CORS?

A security feature that allows or restricts resources on a web page to be requested from another domain.

### Real-life Example:

- **Without CORS**: Can only shop at your local mall
- **With CORS**: Can shop at any mall in the city

### Implementation:

```csharp
services.AddCors(options =>
{
    options.AddPolicy("AllowAll", builder =>
    {
        builder.AllowAnyOrigin()
               .AllowAnyMethod()
               .AllowAnyHeader();
    });
});
```

---

## 🛣️ **MVC Routing**

### What is Routing?

Maps URLs to specific controller actions.

### Types:

1. **Conventional Routing**: Predefined pattern
2. **Attribute Routing**: Routes defined with attributes

### Example:

```csharp
[Route("api/[controller]")]
public class UsersController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetUser(int id) { }
}
```

---

## 📊 **Code First vs Database First**

### Code First:

- Create classes first → Database generated automatically
- **Like**: Building a house from blueprint

### Database First:

- Database exists first → Classes generated automatically
- **Like**: Creating blueprint from existing house

---

## 🔍 **IQueryable vs IEnumerable**

### IEnumerable:

- In-memory operations
- **Like**: Getting all books from library to your table, then filtering

### IQueryable:

- Database operations (more efficient)
- **Like**: Asking librarian to bring only specific books

### Example:

```csharp
// IEnumerable - brings all data to memory
var users = context.Users.ToList().Where(u => u.Age > 18);

// IQueryable - filters at database level
var users = context.Users.Where(u => u.Age > 18).ToList();
```

---

## 🏗️ **SOLID Principles**

### Single Responsibility Principle (S):

A class should have only one reason to change

### Open/Closed Principle (O):

Open for extension, closed for modification

### Liskov Substitution Principle (L):

Subtypes must be substitutable for their base types

### Interface Segregation Principle (I):

Many specific interfaces are better than one general interface

### Dependency Inversion Principle (D):

Depend on abstractions, not concretions

---

## 📚 **SQL Concepts**

### CTE (Common Table Expression):

Temporary named result set

```sql
WITH EmployeeCTE AS (
    SELECT * FROM Employees WHERE Department = 'IT'
)
SELECT * FROM EmployeeCTE WHERE Salary > 50000;
```

### Temp Tables:

Temporary storage in tempdb

```sql
SELECT * INTO #TempEmployees FROM Employees;
```

### Composite Primary Key:

Primary key made of multiple columns

```sql
CREATE TABLE OrderItems (
    OrderID INT,
    ProductID INT,
    PRIMARY KEY (OrderID, ProductID)
);
```

---

## ⚡ **C# Advanced Concepts**

### readonly vs const:

- **const**: Compile-time constant, must be initialized
- **readonly**: Runtime constant, can be set in constructor

### Dictionary vs Hashtable:

- **Dictionary**: Generic, type-safe
- **Hashtable**: Non-generic, slower

### Virtual Class:

Allows method overriding in derived classes

### Sealed Class:

Prevents inheritance

---

## 🔄 **Source Code Management**

### GIT Basics:

```bash
git clone <url>          # Copy repository
git add .               # Stage changes
git commit -m "message" # Commit changes
git push                # Upload to remote
git pull                # Download updates
```

### TFS (Team Foundation Server):

Microsoft's application lifecycle management tool

---

## 📊 **Topic Grading Summary**

### .NET Core: ⭐⭐⭐⭐⭐

- Dependency Injection, Middlewares, Kestrel, Configuration

### Web API: ⭐⭐⭐⭐⭐

- REST principles, HTTP methods, Swagger, JWT, CORS

### ASP.NET MVC: ⭐⭐⭐⭐

- Routing, Filters, Partial Views, ViewBag/ViewData

### SQL: ⭐⭐⭐⭐

- CTE, Temp Tables, Composite Keys, Stored Procedures

### C#: ⭐⭐⭐⭐⭐

- Async/Await, LINQ, Interfaces, Generics, Advanced OOP

### Advanced Concepts: ⭐⭐⭐⭐

- SOLID, Design Patterns, Performance Optimization

### Source Code Management: ⭐⭐⭐

- GIT fundamentals, Branching strategies

---

## 💡 **Interview Tips**

1. **Explain concepts with real-life examples**
2. **Show code examples when possible**
3. **Discuss practical implementation experiences**
4. **Be prepared for scenario-based questions**
5. **Understand the "why" behind each concept**

## 🎯 **Key Areas to Focus**

- Dependency Injection and service lifetimes
- Async programming patterns
- Security (JWT, CORS)
- Database design and optimization
- Clean code principles (SOLID)
- API design and consumption

This guide covers fundamental to advanced concepts typically asked in .NET Core interviews. Practice explaining each concept in simple terms and be ready to write code examples during technical interviews.
