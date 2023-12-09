# Owned Entities & Table Splitting

### Owned Entity Types Nedir?
- EF Core entity sınıflarını parçalayarak, propertylerini kümesel olarak farklı sınıflarda barındırmamıza ve tüm bu sınıfları ilgili entity'de birlkeştirip bütünsel olarak çalışmamıza izin vermektedir.
- Böylece bir entity, sahip olunan(owned) birden fazla alt sınıfın birleşmesiyle meydana gelebilmektedir.


### Owned Entity Types'ı Neden Kullanırız?
https://www.gencayyildiz.com/blog/wp-content/uploads/2020/12/Entity-Framework-Core-Owned-Entities-and-Table-Splitting.png

- Domain Driven Design(DDD) yaklaşımında Value Object'lere karşılık olarak Owned Entity Types'lar kullanılır!

### Owned Entity Types Nasıl Uygulanır?
- Normal bir entity'de farklı sınıfların referans edilmesi primary key vs. gibi hatalara sebebiyet verecektir. Çünkü direkt bir sınfıın referans olarak alınması ef core tarafından ilişkisel bir tasarım olarak algılanır. Bizlerin entity ieçrisindeki propertyleri kümesel olarak barındıran sınıfları o entity'nin bir parçası olduğunu bildirmemiz özellikle gerekmektedir.
```csharp
class Employee
{
    public int Id { get; set; }
    // public string Name { get; set; }
    // public string MiddleName { get; set; }
    // public string LastName { get; set; }
    // public string StreetAddress { get; set; }
    // public string Location { get; set; }
    public bool IsActive { get; set; }

    public EmployeeName EmployeeName { get; set; }
    public Address Adress { get; set; }

    public ICollection<Order> Orders { get; set; }
}
[Owned]
class EmployeeName
{
    public string Name { get; set; }
    public string MiddleName { get; set; }
    public string LastName { get; set; }
}
[Owned]
class Address
{
    public string StreetAddress { get; set; }
    public string Location { get; set; }
}
```
### Owned Attribute'u
```csharp
[Owned]
class EmployeeName
{
	...
}
```
### OwnsOne Metodu 
```csharp
modelBuilder.Entity<Employee>().OwnsOne(e => e.Adress);
```
```csharp
 modelBuilder.Entity<Employee>().OwnsOne(e => e.EmployeeName, builder =>
{
	builder.Property(e => e.Name).HasColumnName("Name");
});
```




### IEntityTypeConfiguration<T> Arayüzü
```csharp
class EmployeeConfiguration : IEntityTypeConfiguration<Employee>
{
    public void Configure(EntityTypeBuilder<Employee> builder)
    {
        builder.OwnsOne(e => e.EmployeeName, builder =>
        {
            builder.Property(e => e.Name).HasColumnName("Name");
        });
        builder.OwnsOne(e => e.Adress);
    }
}
```
- **`OnModelCreating()`** fonksiyonunda uygulanmalıdır.
```csharp
modelBuilder.ApplyConfiguration(new EmployeeConfiguration());
```

### OwnsMany Metodu
- OwnsMany metodu, entity'nin farklı özelliklerine başka bir sınıftan ICollection türünde Navigation Property aracılığıyla ilişkisel olarak erişebilmemizi sağlayan bir işleve sahiptir.
Normalde Has ilişki olarak kurulabilecek bu ilişkinin temel farkı, Has ilişkisi DbSet property'si gerektirirken, OwnsMany metodu ise DbSet'e ihtiyaç duymaksızın gerçekleştirmemizi sağlamaktadır.
```csharp
class Employee
{
    public int Id { get; set; }
    public bool IsActive { get; set; }

    public EmployeeName EmployeeName { get; set; }
    public Address Adress { get; set; }

    public ICollection<Order> Orders { get; set; }
}
class Order
{
    public string OrderDate { get; set; }
    public int Price { get; set; }
}
```
```csharp
modelBuilder.Entity<Employee>().OwnsMany(e => e.Orders, builder =>
{
	builder.WithOwner().HasForeignKey("OwnedEmployeeId");
	builder.Property<int>("Id");
	builder.HasKey("Id");
});
```

