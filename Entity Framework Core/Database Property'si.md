
# Database Property'si


### Database Property'si
- Database property'si veritabanını temsil eden ve EF Core'un bazı işlevlerinin detaylarına erişmemizi sağlayan bir propertydir.

### BeginTransaction
- EF Core, transaction yönetimini otomatik bir şekilde kendisi gerçekleştirmektedir. Eğer ki transaction yönetimini manuel olarak anlık ele almak istiyorsak BeginTransaction fonksiyonunu kullanabiliriz.
```csharp
IDbContextTransaction transaction = context.Database.BeginTransaction();
```
### CommitTransaction
- EF Core üzerinde yapılan çalışmaların commit edilebilmesi için kullanılan bir fonksiyondur.
```csharp
context.Database.CommitTransaction();
```
### RollbackTransaction
- EF Core üzerinde yapılan çalışmaların rollback edilebilmesi için kullanılan bir fonksiyondur.
```csharp
context.Database.RollbackTransaction();
```
### CanConnect
- Verilen connection string'e karşılık bağlantı kurulabilir bir veritabanı var mı yok mu bunun bilgisini bool türde veren bir fonksiyondur.
```csharp
bool connect = context.Database.CanConnect();
Console.WriteLine(connect);
```
### EnsureCreated
- EF Core'da tasarlanan veritabanını migration kullanmaksızın, runtime'da yani kod üzerinde veritabanı sunucusuna inşa edebilmek için kullanılan bir fonksiyondur.
```csharp
context.Database.EnsureCreated();
```
### EnsureDeleted
- İnşa edilmiş veritabanını runtime'da silmemizi sağlayan bir fonksiyondur.
```csharp
context.Database.EnsureDeleted();
```
### GenerateCreateScript
- Context nesnesinde yapılmış olan veritabanı tasarımı her ne ise ona uygun bir SQL Script'ini string olarak veren metottur.
```csharp
var script = context.Database.GenerateCreateScript();
Console.WriteLine(script);
```
### ExecuteSql
- Veritabanına yapılacak Insert, Update ve Delete sorgularını yazdığımız bir metottur. Bu metot işlevsel olarak alacağı parametreleri SQL Injection saldırılarına karşı korumaktadır. 
```csharp
string name = Console.ReadLine();
var result = context.Database.ExecuteSql($"INSERT Persons VALUES('{name}')");
```
### ExecuteSqlRaw
- Veritabanına yapılacak Insert, Update ve Delete sorgularını yazdığımız bir metottur. Bu metotta ise sorguyu SQL Injection saldırılarına karşı koruma görevi geliştirinin sorumluluğundadır.
```csharp
string name = Console.ReadLine();
var result = context.Database.ExecuteSqlRaw($"INSERT Persons VALUES('{name}')");
```
### SqlQuery
- SqlQuery fonksiyonu her ne kadar erişilebilir olsada artık desteklenememktedir. Bunun yerine DbSet propertysi üzerinden erişilebilen FromSql fonksiyonu gelmiştir/kullanılmaktadır.

### SqlQueryRaw
- SqlQueryRaw fonksiyonu her ne kadar erişilebilir olsada artık desteklenememktedir. Bunun yerine DbSet propertysi üzerinden erişilebilen FromSqlRaw fonksiyonu gelmiştir/kullanılmaktadır.

### GetMigrations
- Uygulamada üretilmiş olan tüm migration'ları runtime'da programatik olarak elde etmemizi sağlayan metottur.
```csharp
var migs = context.Database.GetMigrations();
```
### GetAppliedMigrations
- Uygulamada migrate edilmiş olan tüm migrationları elde etmemizi sağlayan bir fonksiyondur.
```csharp
var migs = context.Database.GetAppliedMigrations();
```
### GetPendingMigrations
- Uygulamada migrate edilmemiş olan tüm migrationları elde etmemizi sağlayan bir fonksiyondur.
```csharp
var migs = context.Database.GetPendingMigrations();
```
### Migrate
- Migration'ları programatik olarak runtime'da migrate etmek için kullanılan bir fonksiyondur.
```csharp
context.Database.Migrate();
```
- EnsureCreated fonksiyonu migration'ları kapsamamaktadır. O yüzden migraton'lar içerisinde yapılan çalışmalar ilgili fonksiyonda geçerli olmayacaktır.

### OpenConnection
- Veritabanı bağlantısını manuel açar.
```csharp
context.Database.OpenConnection();
```
### CloseConnection
- Veritabanı bağlantısını manuel kapatır.
```csharp
context.Database.CloseConnection();
```
### GetConnectionString
- İlgili context nesnesinin o anda kullandığı connectionstring değeri ne ise onu elde etmenizi sağlar.
```csharp
Console.WriteLine(context.Database.GetConnectionString());
```
### GetDbConnection
- EF Core'un kullanmış olduğu Ado.NET altyapısının kullandığı DbConnection nesnesini elde etmemizi sağlayan bir fonksiyondur. Yaniiii bizleri Ado.NET kanadına götürür.
```csharp
SqlConnection connection = (SqlConnection)context.Database.GetDbConnection();
```
### SetDbConnection
 - Özelleştirilmiş connection nesnelerini EF Core mimarisine dahil etmemizi sağlayan bir fonksiyondur.
```csharp
context.Database.SetDbConnection();
```
### ProviderName Property'si
- EF Core'un kullanmış olduğu provider neyse onun bilgisini getiren bir proeprty'dir.
```csharp
Console.WriteLine(context.Database.ProviderName);
```

