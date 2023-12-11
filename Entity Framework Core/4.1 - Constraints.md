# Constraints

### Primary Key Constraint

- Bir kolonu PK constraint ile birincil anahtar yapmak istiyorsak eğer bunun için name convention'dan istifade edebiliriz. **`Id`**, **`ID`**, **`EntityNameId`**, **`EntityNameID`** şeklinde tanımlanan tüm propertyler default olarak EF Core tarafından pk constraint olacak şekilde generate edilirler.
- Eğer ki, farklı bir property'e PK özelliğini atamak istiyorsan burada **`HasKey`** Fluent API'ı veya **`Key`** attribute'u ile bu bildirimi iradeli bir şekilde yapmak zorundasın.

#### HasKey Fonksiyonu:
```csharp
modelBuilder.Entity<Blog>()
            .HasKey(b => b.Id);
```
#### Key Attribute'u:
```csharp
[Key]
public int IdKullanici { get; set; } 
```
#### Alternate Keys - HasAlternateKey:
- Bir entity içerisinde PK'e ek olarak her entity instance'ı için alternatif bir benzersiz tanımlayıcı işlevine sahip olan bir key'dir.

```csharp
modelBuilder.Entity<Blog>()   
			.HasAlternateKey(b => b.Url)
			.HasName("ornek");
```
#### Composite Alternate Key:
- Url ve BlogName farklı satırlarda aynı kombinasyonda bulunamazlar.
```csharp
modelBuilder.Entity<Blog>()   
			.HasAlternateKey(b => new { b.Url, b.BlogName });
```

#### HasName Fonksiyonu İle Primary Key Constraint'e İsim Verme:
```csharp
.HasName("ornek");
```

### Foreign Key Constraint
- İki tablo arasında bir ilişki oluşturur ve bu ilişkiye göre bir tablodaki sütunun diğer tablodaki bir sütunu referans almasını sağlar.
#### HasForeignKey Fonksiyonu:
```csharp
modelBuilder.Entity<Blog>()
        .HasMany(b => b.Posts)
        .WithOne(b => b.Blog)
        .HasForeignKey("BlogForeignKeyId")
        .HasConstraintName("ornekforeignkey");
```
#### ForeignKey Attribute'u:
```csharp
[ForeignKey(nameof(Blog))]
public int blog_id { get; set; }

// Navigation Property
public Blog Blog { get; set; }
```

#### Composite Foreign Key:
```csharp
.HasForeignKey(p => new{ p.BlogId, p.BlogUrl });
```

#### Shadow Property Üzerinden Foreign Key:
```csharp
modelBuilder.Entity<Blog>()
            .Property<int>("BlogForeignKeyId");
            
modelBuilder.Entity<Blog>()
            .HasMany(b => b.Posts)
            .WithOne(b => b.Blog)
            .HasForeignKey("BlogForeignKeyId")
            .HasConstraintName("ornekforeignkey");
```


#### HasConstraintName Fonksiyonu İle Foreign Key Constraint'e İsim Verme:
```csharp
.HasConstraintName("ornekforeignkey");
```

### Unique Constraint
- Bir sütundaki değerlerin benzersiz olmasını sağlar, yani aynı değeri içeren birden fazla kayıt eklenemez.

#### HasIndex - IsUnique Fonksiyonları:
```csharp
modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

#### Index, IsUnique Attribute'ları:
```csharp
// Entity class'ına tanımlanır.
[Index(nameof(Blog.Url), IsUnique = true)]
```

#### Alternate Key:
```csharp
modelBuilder.Entity<Blog>()
            .HasAlternateKey(b => b.Url);
```

### Check Constratint
-  Bir sütundaki değerlerin belirli bir koşulu karşılamasını zorlar.
- Sql sorgusuyla çalışır.

#### HasCheckConstraint:
```csharp
modelBuilder.Entity<Post>()
            .HasCheckConstraint("a_b_check_const", "[A] > [B]");
```
