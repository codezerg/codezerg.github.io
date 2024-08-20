# Repository Pattern in C#

## Introduction

The Repository Pattern is a design pattern that mediates between the domain and data mapping layers, acting like an in-memory collection of domain objects. This pattern is particularly useful in complex applications where you want to maintain a clean separation of concerns.

## Without a Repository Pattern

In traditional data access scenarios, business logic often interacts directly with the data access layer. Here's a simplified example:

```csharp
public class BookService
{
    private readonly DbContext _context;

    public BookService(DbContext context)
    {
        _context = context;
    }

    public Book GetBook(int id)
    {
        return _context.Books.Find(id);
    }

    public void AddBook(Book book)
    {
        _context.Books.Add(book);
        _context.SaveChanges();
    }
}
```

## Why This Design is Problematic

1. **Tight coupling**: The `BookService` is directly dependent on the `DbContext`, making it hard to change the data access strategy.
2. **Lack of abstraction**: Any changes in the data layer might affect multiple parts of the application.
3. **Difficult testing**: Unit testing becomes challenging due to direct database dependencies.
4. **Code duplication**: Similar data access code may be repeated across different services.

## Applying the Repository Pattern

The repository pattern introduces an abstraction layer between the business logic and data access layers.

## Benefits of the Repository Pattern

1. **Decoupling**: Separates business logic from data access logic.
2. **Centralized data access logic**: Reduces duplication and improves maintainability.
3. **Easier unit testing**: Allows for mocking or faking repository implementations in tests.
4. **Flexibility**: Enables easier switching between different data sources or ORM frameworks.
5. **Improved organization**: Provides a clean separation of concerns.

## The Example Application

Let's consider a simple application managing a collection of books.

## IRepository<T> Interface

```csharp
public interface IRepository<T> where T : class
{
    T GetById(int id);
    IEnumerable<T> GetAll();
    IEnumerable<T> Find(Expression<Func<T, bool>> predicate);
    void Add(T entity);
    void Update(T entity);
    void Delete(T entity);
    void SaveChanges();
}
```

## Implementing the Repository

```csharp
public class BookRepository : IRepository<Book>
{
    private readonly DbContext _context;
    private readonly DbSet<Book> _books;

    public BookRepository(DbContext context)
    {
        _context = context;
        _books = context.Set<Book>();
    }

    public Book GetById(int id)
    {
        return _books.Find(id);
    }

    public IEnumerable<Book> GetAll()
    {
        return _books.ToList();
    }

    public IEnumerable<Book> Find(Expression<Func<Book, bool>> predicate)
    {
        return _books.Where(predicate);
    }

    public void Add(Book entity)
    {
        _books.Add(entity);
    }

    public void Update(Book entity)
    {
        _books.Attach(entity);
        _context.Entry(entity).State = EntityState.Modified;
    }

    public void Delete(Book entity)
    {
        _books.Remove(entity);
    }

    public void SaveChanges()
    {
        _context.SaveChanges();
    }
}
```

## Testability

The repository pattern greatly enhances testability. Here's an example of a mock repository for testing:

```csharp
public class MockBookRepository : IRepository<Book>
{
    private List<Book> _books = new List<Book>();

    public Book GetById(int id)
    {
        return _books.FirstOrDefault(b => b.Id == id);
    }

    public void Add(Book entity)
    {
        _books.Add(entity);
    }

    // Implement other methods...
}
```

## Best Practices and Common Pitfalls

1. **Do**: Keep repositories focused on data access concerns.
2. **Don't**: Put business logic in repositories.
3. **Do**: Use dependency injection to provide repository instances.
4. **Don't**: Create repository instances directly in your business logic.
5. **Do**: Consider using the Unit of Work pattern alongside repositories for managing transactions.
6. **Don't**: Expose IQueryable from your repositories, as it can lead to tight coupling.

## Summary

The repository pattern provides a powerful abstraction for data access in C# applications. It promotes loose coupling, improves testability, and centralizes data access logic. By implementing this pattern, developers can create more maintainable and flexible applications that are easier to test and evolve over time.

## Further Reading

1. [Martin Fowler on the Repository Pattern](https://martinfowler.com/eaaCatalog/repository.html)
2. [Microsoft Docs: The Repository Pattern](https://docs.microsoft.com/en-us/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)
3. [The Repository Pattern Explained](https://deviq.com/repository-pattern/)
