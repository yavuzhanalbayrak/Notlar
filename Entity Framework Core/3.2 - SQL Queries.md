# SQL Queries

- Eğer ki, sorgunuzu LINQ ile ifade edemiyorsanız yahut LINQ'in ürettiği sorguya nazaran daha optimize bir sorguyu manuel geliştirmek ve EF Core üzerinden execute etmek istiyorsanız EF Core'un bu davrnaışı desteklediğini bilmelisiniz.

- Manuel bir şekilde/tarafımızca oluşturulmuş olan sorguları EF Core tarafından execute edebilmek için o sorgunun sonucunu karşılayacak bir entity model'ın tasarlanmış ve bunun DbSet olarak context nesnesine tanımlanmış olması gerekiyor.
### FromSqlInterpolated
- EF Core 7.0 sürümünden önce ham sorguları execute edebildiğimiz fonksiyondur.
```csharp
var persons = await context.Persons.FromSqlInterpolated($"SELECT * FROM Persons")
    .ToListAsync();
```

### FromSql - EF Core 7.0
- EF Core 7.0 ile gelen metottur. 

#### Query Execute
```csharp
var persons = await context.Persons.FromSql($"SELECT * FROM Persons")
.ToListAsync();
```
#### Stored Procedure Execute
```csharp
var persons = await context.Persons.FromSql($"EXECUTE dbo.sp_GetAllPersons 4")
    .ToListAsync();
```
### Parametreli Sorgu Oluşturma
 **Örnek 1:**
```csharp
int personId = 3;
var persons = await context.Persons.FromSql($"SELECT * FROM Persons Where PersonId = {personId}")
    .ToListAsync();

// Burada sorguya geçirilen personId değişkeni arkaplanda bir DbParameter türüne dönüştürülerek o şekilde sorguya dahil edilmektedir.
```

 **Örnek 2:**
```csharp
int personId = 3;
var persons = await context.Persons.FromSql($"EXECUTE dbo.sp_GetAllPersons {personId}")
    .ToListAsync();
```
**Örnek 3:**
```csharp
SqlParameter personId = new("PersonId", "3");
personId.DbType = System.Data.DbType.Int32;
personId.Direction = System.Data.ParameterDirection.Input;

var persons = await context.Persons.FromSql($"SELECT * FROM Persons Where PersonId = {personId}")
    .ToListAsync();
```
**Örnek 4:**
```csharp
SqlParameter personId = new("PersonId", "3");
var persons = await context.Persons.FromSql($"EXECUTE dbo.sp_GetAllPersons {personId}")
    .ToListAsync();
```
 **Örnek 5:**
```csharp
SqlParameter personId = new("PersonId", "3");
var persons = await context.Persons.FromSql($"EXECUTE dbo.sp_GetAllPersons @PersonId = {personId}")
    .ToListAsync();
```
### Dynamic SQL Oluşturma ve Parametre Girme - FromSqlRaw
- EF Core dinamik olarak oluşturulan sorgularda özellikle kolon isimleri parametreleştirilmişse o sorguyu ÇALIŞTIRMAYACAKTIR!
```csharp
// Hatalı
string columnName = "PersonId", value = "3";
var persons = await context.Persons.FromSql($"Select * From Persons Where {columnName} = {value}")
    .ToListAsync();
```
- Bu hatanın önüne geçmek için:
```csharp
string columnName = "PersonId";
SqlParameter value = new("PersonId", "3");
var persons = await context.Persons.FromSqlRaw($"Select * From Persons Where {columnName} = @PersonId", value)
    .ToListAsync();
```
**Dikkat:**
- FromSql ve FromSqlInterpolated metotlarında SQL Injection vs. gibi güvenlik önlemleri alınmış vaziyettedir. Lakin dinamik olarak sorguları oluşturuyorsanız eğer burada güvenlik geliştirici sorumludur. Yani gelen sorguda/veri yorumlar, noktalı virgüller yahut SQL'e özel karakterlerin algılanması ve bunların temizlenmesi geliştirici tarafından gerekmektedir.
### SqlQuery - Entiy Olmayan Scalar Sorguların Çalıştırılması - Non Entity - EF Core 7.0
- Entity'si olmayan scalar sorguların çalıştırılıp sonucunu elde etmemizi sağlayan yeni bir fonksiyondur.

- SqlQuery'de LINQ operatörleriyle sorguya ekstradan katkıda bulunmak istiyorsanız eğer bu sorgu neticesinde gelecek olan kolonun adını Value olarak bildirmeniz gerekmektedir. Çünkü, SqlQuery metodu sorguyu bir subquery olarak generate etmektedir. Haliyle bu durumdan dolayı LINQ ile verilen şart ifadeleri statik olarka Value kolonuna göre tasarlanmıştır. O yüzden bu şekilde bir çalışma zorunlu gerekmektedir.

```csharp
var data = await context.Database.SqlQuery<int>($"SELECT PersonId FROM Persons")
    .ToListAsync();

var persons = await context.Persons.FromSql($"SELECT * FROM Persons")
    .Where(p => p.Name.Contains("a"))
    .ToListAsync();

var data = await context.Database.SqlQuery<int>($"SELECT PersonId Value FROM Persons")
    .Where(x => x > 5)
    .ToListAsync();
```
### ExecuteSql
- Insert, update, delete
```csharp
await context.Database.ExecuteSqlAsync($"Update Persons SET Name = 'Fatma' WHERE PersonId = 1");
```
### Sınırlılıklar
- Queryler entity türünün tüm özellikleri için kolonlarda değer döndürmelidir.
```csharp
// Hatalı
var persons = await context.Persons.FromSql($"SELECT Name, PersonId FROM Persons")
    .ToListAsync();
```
- Sütun isimleri property isimleriyle aynı olmalıdır.

- SQL Sorgusu Join yapısı İÇEREMEZ!!! Haliyle bu tarz ihtiyaç noktalarında Include fonksiyonu KULLANILMALIDIR!
```csharp
var persons = await context.Persons.FromSql($"SELECT * FROM Persons")
    .Include(p => p.Orders)
    .ToListAsync();
```

