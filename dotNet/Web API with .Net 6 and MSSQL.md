
# Web API with .Net 6 and MSSQL

Here is the package manager console's commands wrote down
in step by step to create a project and connect it with database.


## Command List

### ---Packages to install---

```bash
Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
Install-Package Microsoft.EntityFrameworkCore.Tools
```

### ---Creating db context and Models from DB to Models Folder---

```bash
Scaffold-DbContext "Server=192.168.4.9;Database=ToDoDb;User Id=sa;Password=sql@123;Trusted_Connection=True; TrustServerCertificate=True; Integrated Security=False;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

### ---Add Connection string in AppSettings---

```bash
"ConnectionStrings":{
	"DefaultConnection":"Server=192.168.4.9;Database=ToDoDb;User Id=sa;Password=sql@123;Trusted_Connection=True;Integrated Security=False;"
}

```

### ---Add initial migration---

```bash
add-migration initial
```

### ---Add builder service of sql in Program.cs to Connect the DATABASE---

```bash
builder.Services.AddDbContext<ToDoDbContext>(opt => opt.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```

### ---Update db context and Models from DB---
```bash
Scaffold-DbContext "Server=192.168.4.9;Database=ToDoDb;User Id=sa;Password=sql@123;Trusted_Connection=True;Integrated Security=False;" Microsoft.EntityFrameworkCore.SqlServer -NoPluralize -force -OutputDir Models
```
