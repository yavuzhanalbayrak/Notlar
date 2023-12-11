# İlişkisel Tablolarda Veri Silme
### One to One İlişkisel Senaryolarda Veri Silme

```csharp
Person? person = await context.Persons
    .Include(p => p.Address)
    .FirstOrDefaultAsync(p => p.Id == 1);

if (person != null)
    context.Addresses.Remove(person.Address);
await context.SaveChangesAsync();
```
### One to Many İlişkisel Senaryolarda Veri Silme

```csharp
Blog? blog = await context.Blogs
    .Include(b => b.Posts)
    .FirstOrDefaultAsync(b => b.Id == 1);
Post? post = blog.Posts.FirstOrDefault(p => p.Id == 2);

context.Posts.Remove(post);
await context.SaveChangesAsync();
```
### Many to Many İlişkisel Senaryolarda Veri Silme

```csharp
Book? book = await context.Books
    .Include(b => b.Authors)
    .FirstOrDefaultAsync(b => b.Id == 2);

Author? author = book.Authors.FirstOrDefault(a => a.Id == 2);
//context.Authors.Remove(author); //Yazarı silmeye kalkar!!!
book.Authors.Remove(author);
await context.SaveChangesAsync();
```
### Cascade Delete

- Bu davranış modelleri Fluent API ile konfigüre edilebilmektedir.
- Esas tablodan silinen veriyle karşı/bağımlı tabloda bulunan ilişkili verilerin silinmesini sağlar.
- Default olarak seçili ayardır.
- Many to Many ilişkilerde sadece Cascade Delete'e izin verilir.
```csharp
modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .IsRequired(false)
            .OnDelete(DeleteBehavior.Cascade);
```
### SetNull
- Esas tablodan silinen veriyle karşı/bağımlı tabloda bulunan ilişkili verilere null değerin atanmasını sağlar.
- One to One senaryolarda eğer ki Foreign key ve Primary key kolonları aynı ise o zaman SetNull davranışını KULLANAMAYIZ!
```csharp
modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .IsRequired(false)
            .OnDelete(DeleteBehavior.SetNull);
```
### Restrict
- Esas tablodan herhangi bir veri silinmeye çalışıldığında o veriye karşılık dependent table'da ilişkisel veri/ler varsa eğer bu sefer bu silme işlemini engellemesini sağlar.
```csharp
modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .IsRequired(false)
            .OnDelete(DeleteBehavior.Restrict);
```
