
# Create a web API with ASP.NET Core and MongoDB

This tutorial creates a web API that runs CRUD operations on a MongoDB NoSQL database following official microsoft official
[documentation](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-mongo-app?view=aspnetcore-6.0&tabs=visual-studio-code).
Although i have followed official documentation but i've modified it little bit for managing multiple MongoDB Collection by following this 
[tutorial](https://youtu.be/gz5ls6aMhZQ).


## Prerequisites
I've used vs code instead of visual studio. so for vs code following things needed to develop the API.

- C# extenstion for VScode
- .NET 6.0 SDK
- MongoDB database and some collection


## Process to follow

#### 1. Create the ASP.NET Core web API project
```bash
dotnet new webapi -o BookStoreApi
code BookStoreApi
```
The preceding commands generate a new ASP.NET Core web API project and then open the project in Visual Studio Code.

#### 2. Add Required assets to build and debug

Once the OmniSharp server starts up , a dialog asks `Required assets to build and debug are missing from 'BookStoreApi'. Add them?`. Select `Yes`.

#### 3. Add MongoDB driver 

Open the Integrated Terminal and run the following command to install the .NET driver for MongoDB.
```bash
dotnet add package MongoDB.Driver
```

#### 4. Add Entity Model

Add  required model classes into **Models** directory to the project root. Here is an example.
```bash
using MongoDB.Bson;
using MongoDB.Bson.Serialization.Attributes;

namespace RestApiWithMongoDb.Models;

public class Book
{
    [BsonId]
    [BsonRepresentation(BsonType.ObjectId)]
    public string? Id { get; set; }

    [BsonElement("Name")]
    public string BookName { get; set; } = null!;

    public decimal Price { get; set; }

    public string Category { get; set; } = null!;

    public string Author { get; set; } = null!;
}
```
**5. Add Database configuration

Add `DatabaseConnection` value into the `appsettings.json` file.

```bash
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",

  "DatabaseConnection": {
    "ConnectionString": "mongodb://admin:867@localhost:27000",
    "DatabaseName": "BookStore"
  }
}
```
#### 6. Add a `DatabaseSettings` class to the `Models` directory

```bash
namespace RestApiWithMongoDb.Models;

public class DatabaseSettings
{
    public string ConnectionString { get; set; } = null!;

    public string DatabaseName { get; set; } = null!;
}
```
The preceding `DatabaseSettings` class is used to store the appsettings.json file's `DatabaseConnection` property values. The JSON and C# property names are kept same for the mapping process.

#### 7. Add following code to `Program.cs`

```bash
var builder = WebApplication.CreateBuilder(args);

//Add database connection with singleton
builder.Services.AddSingleton<IMongoDatabase>(option=>{
    var mongoSettings= builder.Configuration.GetSection("DatabaseConnection").Get<DatabaseSettings>();
    var mongoClient = new MongoClient(mongoSettings.ConnectionString);
    return mongoClient.GetDatabase(mongoSettings.DatabaseName);
});
```
Here, the `DatabaseSettings` object's `ConnectionString` property is populated with the `DatabaseConnection : ConnectionString` property in appsettings.json.

#### 8. Add CRUD operations services

Add a `BooksService` class to the **Services** directory with the following code:

```bash
using RestApiWithMongoDb.Models;
using Microsoft.Extensions.Options;
using MongoDB.Driver;

namespace RestApiWithMongoDb.Services;

public class BooksService
{
    private readonly IMongoCollection<Book> _booksCollection;

    public BooksService(IMongoDatabase mongoDatabase)
    {
        _booksCollection = mongoDatabase.GetCollection<Book>("Books"); //put collection name inside "Books"
    }

    public async Task<List<Book>> GetAsync() =>
        await _booksCollection.Find(_ => true).ToListAsync();

    public async Task<Book?> GetAsync(string id) =>
        await _booksCollection.Find(x => x.Id == id).FirstOrDefaultAsync();

    public async Task CreateAsync(Book newBook) =>
        await _booksCollection.InsertOneAsync(newBook);

    public async Task UpdateAsync(string id, Book updatedBook) =>
        await _booksCollection.ReplaceOneAsync(x => x.Id == id, updatedBook);

    public async Task RemoveAsync(string id) =>
        await _booksCollection.DeleteOneAsync(x => x.Id == id);
}
```

#### 9. Add the following highlighted code to `Program.cs`

Put the following code under number 7 code.
```bash
//List of Services added here as singleton

builder.Services.AddSingleton<BooksService>();
```

#### 10. Add a `BooksController` class to the `Controllers` directory
```bash
using RestApiWithMongoDb.Models;
using RestApiWithMongoDb.Services;
using Microsoft.AspNetCore.Mvc;

namespace BookStoreApi.Controllers;

[ApiController]
[Route("api/[controller]")]
public class BooksController : ControllerBase
{
    private readonly BooksService _booksService;

    public BooksController(BooksService booksService) =>
        _booksService = booksService;

    [HttpGet]
    public async Task<List<Book>> Get() =>
        await _booksService.GetAsync();

    [HttpGet("{id:length(24)}")]
    public async Task<ActionResult<Book>> Get(string id)
    {
        var book = await _booksService.GetAsync(id);

        if (book is null)
        {
            return NotFound();
        }

        return book;
    }

    [HttpPost]
    public async Task<IActionResult> Post(Book newBook)
    {
        await _booksService.CreateAsync(newBook);

        return CreatedAtAction(nameof(Get), new { id = newBook.Id }, newBook);
    }

    [HttpPut("{id:length(24)}")]
    public async Task<IActionResult> Update(string id, Book updatedBook)
    {
        var book = await _booksService.GetAsync(id);

        if (book is null)
        {
            return NotFound();
        }

        updatedBook.Id = book.Id;

        await _booksService.UpdateAsync(id, updatedBook);

        return NoContent();
    }

    [HttpDelete("{id:length(24)}")]
    public async Task<IActionResult> Delete(string id)
    {
        var book = await _booksService.GetAsync(id);

        if (book is null)
        {
            return NotFound();
        }

        await _booksService.RemoveAsync(id);

        return NoContent();
    }
}
```


## Acknowledgements

 - [Documentation](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-mongo-app?view=aspnetcore-6.0&tabs=visual-studio-code)
 - [Youtube tutorial](https://youtu.be/gz5ls6aMhZQ)

