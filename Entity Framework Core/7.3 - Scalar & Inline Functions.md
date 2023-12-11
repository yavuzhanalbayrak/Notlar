# Scalar & Inline Functions

### Scalar Functions Nedir?
- Geriye herhangi bir türde değer döndüren fonksiyonlardır.

### Scalar Function Oluşturma
**1. adım :** boş bir migration oluşturulmalı.

**2. adım :** bu migration içerisinde Up metodunda Sql metodu eşliğinde fonksiyonun create kodları yazılacak, Down metodu içerisinde de Drop kodları yazılacaktır.
```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
	migrationBuilder.Sql($@"
                    CREATE FUNCTION getPersonTotalOrderPrice(@personId INT)
	                    RETURNS INT
                    AS
                    BEGIN
	                    DECLARE @totalPrice INT
	                    SELECT @totalPrice = SUM(o.Price) FROM Persons p
	                    JOIN Orders o
		                    ON p.PersonId = o.PersonId
	                    WHERE p.PersonId = @personId
	                    RETURN @totalPrice
                    END
                    ");
}        
```
```csharp
protected override void Down(MigrationBuilder migrationBuilder)
{
	migrationBuilder.Sql($@"DROP FUNCTION getPersonTotalOrderPrice");
}
```
**3. adım :** migrate edilmeli.

### Scalar Function'ı EF Core'a Entegre Etme

#### HasDbFunction
- Veritabanı seviyesindeki herhangi bir fonksiyonu EF Core/yazılım kısmında bir metoda bind etmemizi sağlayan fonksiyondur.

- Bunun yanısıra önce bir tane de scalar fonksiyonunu temsil eden imza tanımlanmalıdır.
```csharp
public int GetPersonTotalOrderPrice(int personId)
        => throw new Exception();
```
- **`OnModelCreating()`** fonksiyonu içerisinde **`HasDbFunction`** ile imzayı Configure etmemiz gerek.
```csharp
modelBuilder.HasDbFunction(typeof(ApplicationDbContext).GetMethod(nameof(ApplicationDbContext.GetPersonTotalOrderPrice),  new[]  {  typeof(int)  }))  
			.HasName("getPersonTotalOrderPrice");
```


**Kullanımı:**
```csharp
var persons = await (from person in context.Persons
                     where context.GetPersonTotalOrderPrice(person.PersonId) > 500
                     select person).ToListAsync();
```
-----------------------

### Inline Functions Nedir?
- Geriye bir değer değil, tablo döndüren fonksiyonlardır.

### Inline Function Oluşturma
**1. adım :** boş bir migration oluşturunuz.

**2. adım :** bu migration içerisindeki Up fonksiyonunda Create işlemini, down fonksiyonunda ise drop işlemlerini gerçekleştiriniz.
```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
	migrationBuilder.Sql($@"
	                CREATE FUNCTION bestSellingStaff(@totalOrderPrice INT = 10000)
		                RETURNS TABLE
	                AS
	                RETURN 
	                SELECT TOP 1 p.Name, COUNT(*) OrderCount, SUM(o.Price) TotalOrderPrice FROM Persons p
	                JOIN Orders o
		                ON p.PersonId = o.PersonId
	                GROUP By p.Name
	                HAVING SUM(o.Price) < @totalOrderPrice
	                ORDER By OrderCount DESC
                    ");
}
```
```csharp
protected override void Down(MigrationBuilder migrationBuilder)
{
	migrationBuilder.Sql($@"DROP FUNCTION bestSellingStaff");
}
```
**3. adım :** migrate ediniz.

### Inline Function'ı EF Core'a Entegre Etme
- Öncelikle gelecek olan tabloyu karşılayabilecek bir model'a(**`ModelBinding`**) ihtiyacımız var.
```csharp
public class BestSellingStaff
{
    public string Name { get; set; }
    public int OrderCount { get; set; }
    public int TotalOrderPrice { get; set; }
}
```
- Sonrasında Inline function temsil eden imza oluşturmamız gerek.
```csharp
public IQueryable<BestSellingStaff> BestSellingStaff(int totalOrderPrice = 10000)
         => FromExpression(() => BestSellingStaff(totalOrderPrice));
```
- **`OnModelCreating()`** fonksiyonu içerisinde **`HasDbFunction`** ile imzayı Configure etmemiz gerek.
```csharp
modelBuilder.HasDbFunction(typeof(ApplicationDbContext).GetMethod(nameof(ApplicationDbContext.BestSellingStaff), new[] { typeof(int) }))
            .HasName("bestSellingStaff");

        modelBuilder.Entity<BestSellingStaff>()
            .HasNoKey();
```
**Kullanımı:**
```csharp
var persons = await context.BestSellingStaff(3000).ToListAsync();
foreach (var person in persons)
{
    Console.WriteLine($"Name : {person.Name} | Order Count : {person.OrderCount} | Total Order Price : {person.TotalOrderPrice}");
}
```

