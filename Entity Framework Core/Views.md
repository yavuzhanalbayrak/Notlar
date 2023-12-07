# Views
### View Nedir?
- Oluşturduğumuz kompleks sorguları ihtiyaç durumlarında daha rahat bir şekilde kullanabilmek için basitleştiren bir veritabanı objesidir.

### EF Core İle View Kullanımı

#### View Oluşturma:
**1. adım :** boş bir migration oluşturulmalıdır.

**2. adım :** migration içerisindeki Up fonksiyonunda view'in create komutları, down fonksiyonunda ise drop komutları yazılmalıdır.
- Migration içerisindeki **`Up`** fonksiyonunda view'in **`create`** komutları yazılmalıdır.
```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
	migrationBuilder.Sql($@"
                        CREATE VIEW vm_PersonOrders
                        AS
	                        SELECT TOP 100 p.Name, COUNT(*) [Count] FROM Persons p
	                        INNER JOIN Orders o
		                        ON p.PersonId = o.PersonId
	                        GROUP By p.Name
	                        ORDER By [Count] DESC
                        ");
}
```
- **`down`** fonksiyonunda ise **`drop`** komutları yazılmalıdır.
```csharp
protected override void Down(MigrationBuilder migrationBuilder)
{
	migrationBuilder.Sql($@"DROP VIEW vm_PersonOrders");
}
```

**3. adım :** migrate ediniz.


#### View'i DbSet Olarak Ayarlama
- View'i EF Core üzerinden sorgulayabilmek için view neticesini karşılayabilecek bir entity olşturulması ve bu entity türünden DbSet property'sinin eklenmesi gerekmektedir.
```csharp
public DbSet<PersonOrder> PersonOrders { get; set; }
```

#### DbSet'in Bir View Olduğunu Bildirmek
```csharp
modelBuilder.Entity<PersonOrder>()
            .ToView("vm_PersonOrders")
            .HasNoKey();
```
```csharp
var personOrders = await context.PersonOrders
    .Where(po => po.Count > 10)
    .ToListAsync();
```

### EF Core'da View'lerin Özellikleri
- Viewlerde primary key olmaz! Bu yüzden ilgili DbSet'in HasNoKey ile işaretlenmesi gerekemktedir.
- View neticesinde gelen veriler Change Tracker ile takip edilmezler. Haliyle üzerlerinde yapılan değişiklikleri EF Core veritabanına yansıtmaz
```csharp
// Geçersiz.
var personOrder = await context.PersonOrders.FirstAsync();
personOrder.Name = "Abuzer";
await context.SaveChangesAsync();
```

