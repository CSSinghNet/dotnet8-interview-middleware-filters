# .NET 8 Filters & Middleware -- Senior-Level Guide

This README provides a deep, production-focused overview of **Filters
and Middleware in .NET 8 / ASP.NET Core**, including architecture
diagrams, execution flow, best practices, and sample project structure.

------------------------------------------------------------------------

## 📌 Table of Contents

1.  Middleware in .NET 8
2.  Creating Custom Middleware
3.  Middleware Order Importance
4.  Use vs Run vs Map
5.  Filters in .NET 8
6.  Types of Filters
7.  Execution Order of Filters
8.  Middleware vs Filters
9.  Middleware vs Endpoint Filters
10. Pipeline Flow Diagram
11. Production Best Practices
12. Sample Project Structure
13. Interview Quick Answers

------------------------------------------------------------------------

# 🔹 Middleware in .NET 8

Middleware are components assembled into an HTTP request pipeline to
handle requests and responses.

## Common Use Cases

-   Logging
-   Authentication
-   Authorization
-   Exception handling
-   CORS
-   Request/Response transformation

------------------------------------------------------------------------

# 🔹 Creating Custom Middleware

``` csharp
public class RequestLoggingMiddleware
{
    private readonly RequestDelegate _next;

    public RequestLoggingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        Console.WriteLine($"Incoming: {context.Request.Path}");
        await _next(context);
        Console.WriteLine($"Outgoing: {context.Response.StatusCode}");
    }
}
```

### Register Middleware

``` csharp
app.UseMiddleware<RequestLoggingMiddleware>();
```

------------------------------------------------------------------------

# 🔹 Middleware Order Importance

Correct order ensures routing, auth, and exception handling work
properly.

``` csharp
app.UseExceptionHandler();
app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseRouting();
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
```

------------------------------------------------------------------------

# 🔹 Use vs Run vs Map

  Method   Behavior
  -------- ----------------------
  Use      Continue pipeline
  Run      Terminates pipeline
  Map      Path-based branching

------------------------------------------------------------------------

# 🔹 Filters in .NET 8

Filters run inside MVC pipeline and are used for validation,
authorization, logging, and response handling.

------------------------------------------------------------------------

# 🔹 Types of Filters

1.  Authorization Filters\
2.  Resource Filters\
3.  Action Filters\
4.  Exception Filters\
5.  Result Filters

------------------------------------------------------------------------

# 🔹 Execution Order

Authorization → Resource → Action → Action Method → Result → Exception

------------------------------------------------------------------------

# 🔹 Middleware vs Filters

  Feature           Middleware      Filters
  ----------------- --------------- --------------
  Scope             Global          MVC-specific
  Level             HTTP pipeline   MVC pipeline
  Access to model   ❌              ✅

------------------------------------------------------------------------

# 🔹 Middleware vs Endpoint Filters

  Feature      Middleware           Endpoint Filters
  ------------ -------------------- -------------------
  Level        HTTP pipeline        Endpoint level
  Scope        Global               Specific endpoint
  Works with   MVC + Minimal APIs   Minimal APIs

------------------------------------------------------------------------

# 🔥 Pipeline Flow Diagram

    Incoming Request
          ↓
    Exception Middleware
          ↓
    HTTPS Redirection
          ↓
    Static Files
          ↓
    Routing
          ↓
    Authentication
          ↓
    Authorization
          ↓
    Filters (MVC / Endpoint)
          ↓
    Controller / Minimal API
          ↓
    Filters
          ↓
    Middleware (response flow)
          ↓
    Outgoing Response

------------------------------------------------------------------------

# 🚀 Production Best Practices

## Middleware

-   Keep middleware lightweight
-   Avoid DB calls in middleware
-   Place exception middleware at top
-   Security middleware before routing
-   Use extension methods for registration

## Filters

-   Use Authorization filters for access control
-   Use Action filters for validation/logging
-   Use Exception filters for MVC error handling
-   Avoid heavy business logic inside filters

## Architecture

-   Global concern → Middleware
-   MVC concern → Filters
-   Endpoint concern → Endpoint Filters
-   Follow clean separation of responsibilities

------------------------------------------------------------------------

# 🧱 Sample Project Structure

    src/
     ├── API/
     │    ├── Controllers/
     │    ├── Filters/
     │    │     ├── CustomAuthFilter.cs
     │    │     ├── LoggingActionFilter.cs
     │    │     └── ExceptionFilter.cs
     │    ├── Middleware/
     │    │     ├── ExceptionMiddleware.cs
     │    │     ├── LoggingMiddleware.cs
     │    │     └── TimingMiddleware.cs
     │    ├── Extensions/
     │    │     └── MiddlewareExtensions.cs
     │    └── Program.cs
     │
     ├── Application/
     ├── Domain/
     └── Infrastructure/

------------------------------------------------------------------------

# 🎯 Interview Quick Answers

**Why middleware order matters?**\
Middleware executes sequentially; wrong order breaks routing/auth.

**Middleware vs Filters?**\
Middleware = global HTTP pipeline. Filters = MVC pipeline.

**Use vs Run vs Map?**\
Use → continue, Run → terminate, Map → branch.

**Middleware vs Endpoint filters?**\
Middleware = app-level, Endpoint filter = endpoint-level.

------------------------------------------------------------------------

## 👨‍💻 Target Audience

-   Backend developers
-   .NET 8 engineers
-   Interview preparation
-   Production architecture learning

------------------------------------------------------------------------
