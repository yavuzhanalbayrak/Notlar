# Stored Procedures

### Stored Procedure Nedir?
- SP, view'ler gibi kompleks sorgularımızı daha basit bir şekilde tekrar kullanılabilir  bir hale getirmemizi sağlayan veritabanı nesnesidir. 

- View'ler tablo misali bir davranış sergilerken, SP'lar ise fonksiyonel bir davranış sergilerler.
- Ve türlü türlü artılarıda vardır.

### EF Core İle Stored Procedure Kullanımı
#### Stored Procedure Oluşturma
**1. adım :** boş bir migration oluşturunuz.

**2. adım :** migration'ın içerisindeki Up fonksiyonuna SP'ın Create komutlarını yazınız, Down fonk. ise Drop komutlarını yazınız.
```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
	migrationBuilder.Sql($@"
                    CREATE PROCEDURE sp_PersonOrders2
                    (
	                    @PersonId INT,
	                    @Name NVARCHAR(MAX) OUTPUT
                    )
                    AS
                    SELECT @Name = p.Name FROM Persons p
                    JOIN Orders o
                        ON p.PersonId = o.PersonId
                    WHERE p.PersonId = @PersonId
	                ");
}
```
```csharp
protected override void Down(MigrationBuilder migrationBuilder)
{
	migrationBuilder.Sql($"DROP PROC sp_PersonOrders2");
}
```
**3. adım :** migrate ediniz.

### Stored Procedure'ü Kullanma
- SP'ı kullanabilmek için bir entity'e ihtiyacımız vardır. Bu entity'nin DbSet propertysi ollarak context nesnesine de eklenmesi gerekmektedir.
```csharp
[NotMapped]
public class PersonOrder
{
    public string Name { get; set; }
    public int Count { get; set; }
}
```
```csharp
public DbSet<PersonOrder> PersonOrders { get; set; }
```

- `[NotMapped]` özniteliği (attribute), Entity Framework'te kullanılan bir özniteliktir ve belirli bir sınıf özelliğini veya sınıfın kendisini veritabanında bir sütunla/tabloyla eşleştirmemek için kullanılır.

- Bu Entity'e **`HasNoKey()`** fonksiyonunu eklemeliyiz.
```csharp
modelBuilder.Entity<PersonOrder>()  
					   .HasNoKey();
```

- Bu DbSet properytysi üzerinden FromSql fonksiyonunu kullanarak 'Exec ....' komutu eşliğinde SP yapılanmasını çalıştırıp sorgu neticesini elde edebilirsiniz.

### FromSql
```csharp
var datas = await context.PersonOrders.FromSql($"EXEC sp_PersonOrders")
    .ToListAsync();
```
### Geriye Değer Döndüren Stored Procedure'ü Kullanma
```csharp
SqlParameter countParameter = new()
{
    ParameterName = "count",
    SqlDbType = System.Data.SqlDbType.Int,
    Direction = System.Data.ParameterDirection.Output
};
await context.Database.ExecuteSqlRawAsync($"EXEC @count = sp_bestSellingStaff", countParameter);
Console.WriteLine(countParameter.Value);
```

### Parametreli Stored Procedure Kullanımı
#### Input Parametreli / Output Parametreli Stored Procedure'ü Kullanma

```csharp
SqlParameter nameParameter = new()
{
    ParameterName = "name",
    SqlDbType = System.Data.SqlDbType.NVarChar,
    Direction = System.Data.ParameterDirection.Output,
    Size = 1000
};
await context.Database.ExecuteSqlRawAsync($"EXECUTE sp_PersonOrders2 7, @name OUTPUT", nameParameter);
Console.WriteLine(nameParameter.Value);
```

