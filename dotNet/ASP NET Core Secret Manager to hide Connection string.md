
# ASP.Net Core Secret Manager for DB connection & others

A brief description of how to hide db connection string from `appsetting.json` to `secret.json` file.


## Process

1. Right click on Project Name under Solution and select _`Manage User Secrets`_. This will open a **secrets.json** file. This file lives outside of the projects folder.
2. If we edit project file by Right Clicking on the project we will see this file reference by a id.
```csproj
<UserSecretsId>cd8b9772-b2b1-473e-9a8b-e0bc4e1570d1</UserSecretsId>
```
3. Now copy `DB ConnectionString` into **secrets.json**  file.
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=202.126.122.115;Database=ToDoDb;User Id=sa;Password=Retail@production;Trusted_Connection=True;Integrated Security=False;"
  }
}
```
4. Now run the project. Project will work same way as before. We don't have to write extra code
because `IConfiguration` service of asp.net core  is setup to read configuration information from all the various
configuration sources. 

5. This is the heirarchy how configuration source file will be searched and later
configuration sources *overrride* the earlier configuration sources.

    1. appsettings.json
    2. User Secrets
    3. Environment variable
    4. Command-line argument 

6. ConnectionString also can be provided by `Environment variable` as described on the tutorial.
## Acknowledgements

 - [video tutorial](https://youtu.be/TVF9o5qbrkI)

