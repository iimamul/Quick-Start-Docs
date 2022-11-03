
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


## Acknowledgements

 - [webapi blog](https://www.c-sharpcorner.com/article/rest-api-with-asp-net-6-and-mysql/)
 
