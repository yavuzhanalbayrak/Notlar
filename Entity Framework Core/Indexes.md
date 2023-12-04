# Indexes
### Index Nedir?
- Index, bir sütuna dayalı sorgulamaları daha verimli ve performanslı hale getirmek için kullanılan yapıdır.
- PK, FK ve AK olan kolonlar otomatik olarak indexlenir. 

#### Index Attribute'u:
```csharp
[Index(nameof(Name))]
```

#### HasIndex Metodu:
```csharp
modelBuilder.Entity<Employee>()
            .HasIndex(x => x.Name);
```
#### Composite Index:
```csharp
[Index(nameof(Name), nameof(Surname))]
// Kullanım alanı
context.Employees.Where(e => e.Name == "" || e.Surname == "")
```
#### Birden Fazla Index Tanımlama:
```csharp
modelBuilder.Entity<Employee>()
        .HasIndex(x => x.Name);
modelBuilder.Entity<Employee>()
        .HasIndex(x => x.Surname);
modelBuilder.Entity<Employee>()
        .HasIndex(x => new { x.Name, x.Surname });
```
```csharp
[Index(nameof(Name))]
[Index(nameof(Surname))]
[Index(nameof(Name), nameof(Surname))]
```

#### Index Uniqueness:
```csharp
 modelBuilder.Entity<Employee>()
        .HasIndex(x => x.Name)
        .IsUnique();
```
### Index Sort Order - Sıralama Düzeni (EF Core 7.0)

#### AllDescending - Attribute:
Tüm indexlemelerde descending davranışının bütünsel olarak konfigürasyonunu sağlar.
```csharp
[Index(nameof(Name), AllDescending = true)]
```
#### IsDescending - Attribute:
Indexleme sürecindeki her bir kolona göre sıralama davranışını hususi ayarlamak istiyorsak kullanılır.

```csharp
[Index(nameof(Name), nameof(Surname), IsDescending = new[] { true, false })]
```
#### IsDescending Metodu:
```csharp
modelBuilder.Entity<Employee>()
            .HasIndex(x => x.Name)
            .IsDescending();

modelBuilder.Entity<Employee>()
            .HasIndex(x => new { x.Name, x.Surname })
            .IsDescending(true, false);
```
#### Index Name:
```csharp
modelBuilder.Entity<Employee>()
            .HasIndex(x => x.Name)
            .HasDatabaseName("name_index");
```
```csharp
[Index(nameof(Name), Name = "name_index")]
```

#### HasFilter Metodu:
```csharp
modelBuilder.Entity<Employee>()
            .HasIndex(x => x.Name)
            .HasFilter("[NAME] IS NOT NULL");
```

#### IncludeProperties Metodu:
```csharp
modelBuilder.Entity<Employee>()
            .HasIndex(x => new { x.Name, x.Surname })
            .IncludeProperties(x => x.Salary);
```


