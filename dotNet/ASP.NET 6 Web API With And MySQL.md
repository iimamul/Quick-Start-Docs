
# ASP.NET 6 Web API With And MySQL

A brief description of how to create a .net web api project with mysql and necessary dependency.


## Project Preparation

1. Create a ASP.NET Core Web API project on .NET 6.0
2. Install these NuGet packages.
- Microsoft.EntityFrameworkCore.Design
- Microsoft.EntityFrameworkCore.Tools
- MySql.EntityFrameworkCore

3. Scaffold-DbContext from the existing MySql database.
```bash
Scaffold-DbContext "server=servername;port=portnumber;user=username;password=pass;database=databasename" MySql.EntityFrameworkCore -OutputDir Entities -f
```
4. If `MySql.EntityFrameworkCore` throws and error while scaffold then most probably it's for NuGet packages version.
Downgrade all three packages into version 5.0.16 as mentioned in this [documentation](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core-scaffold-example.html).After successful scaffolding upgrade these packages back to the their latest versions.

5. After successfully scaffolding we'll find connection string into newly created DbContext file.
```cs
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
  if (!optionsBuilder.IsConfigured)
  { optionsBuilder.UseMySQL("server=localhost;port=3306;user=root;password=;database=database");
  }
}
```
6. But it's not appropriate that the connection string to the database is specified in the OnConfiguring method. So we'll move connection string into `appsettings.json` file.
```json
"ConnectionStrings": {
  "DefaultConnection": "server=localhost;port=3306;user=root;password=;database=demo;"
}
```
7. Then, in the `Program.cs`, we'll add as a service to refer DBContext, and that must have the reference of `DefaultConnection` specified in the `appsettings.json` file.
```cs
using Microsoft.EntityFrameworkCore;
using dotNetWithMySqlAPI.Entities;
using MySql.EntityFrameworkCore.Extensions;

builder.Services.AddEntityFrameworkMySQL().AddDbContext<dotnetapiContext>(options => {
    options.UseMySQL(builder.Configuration.GetConnectionString("DefaultConnection"));
});
```
8. Now add a api controller using the model and run the application.
9. Create `UserDTO` model inside `DTO` folder to get and return user request instead of model classes. 
10. To map data automatically from `User` model data into `UserDTO` we can use `AutoMapper`. Install Automapper and Automapper extension for dependency injection in `program.cs`
- AutoMapper
- AutoMapper.Extensions.Microsoft.DependencyInjection
11. Add `MappingProfile.cs` class inside MappingProfiles Folder.
```cs
using AutoMapper;
using dotNetWithMySqlAPI.DTO;
using dotNetWithMySqlAPI.Entities;

namespace dotNetWithMySqlAPI.MappingProfiles
{
    public class MappingProfile:Profile
    {
        public MappingProfile()
        {
            CreateMap<User, UserDTO>();
        }
    }
}
```
12. For dependency injection add this services in `Program.cs`
```cs
//Automapper 
builder.Services.AddAutoMapper(typeof(MappingProfile));
```
13. Now add the following code in the controller.
```cs
private readonly dotnetapiContext _context;
private readonly IMapper _mapper;
public UsersController(dotnetapiContext context, IMapper mapper )
{
    _context = context;
    _mapper = mapper;
}

[HttpGet]
public async Task<ActionResult<IEnumerable<UserDTO>>> GetUsers()
{
    var users = await _context.Users.ToListAsync();
    return _mapper.Map<List<User>,List<UserDTO>>(users);
}
```

## Acknowledgements

 - [webapi blog](https://www.c-sharpcorner.com/article/rest-api-with-asp-net-6-and-mysql/)
 - [Automapper](https://youtu.be/39I1-wwbF7A)
 
