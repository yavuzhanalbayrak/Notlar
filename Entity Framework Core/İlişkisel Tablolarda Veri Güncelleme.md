# İlişkisel Tablolarda Veri Güncelleme
### One to One İlişkisel Senaryolarda Veri Güncelleme
- Person (Principal)
- Address (Dependent)
#### 1. Durum | Asıl tablodaki veriye bağımlı veriyi değiştirme

```csharp
using (var context = new YourDbContext())
{
    // Güncellenecek kişiyi seç
    var person = context.People.Include(p => p.Address).FirstOrDefault(p => p.PersonId == 1);

    if (person != null)
    {
        // Dependent tarafı güncelle
        person.Address.Street = "New Street";
        person.Address.City = "New City";

        // Değişiklikleri kaydet
        context.SaveChanges();
    }
}

```
#### 2. Durum | Bağımlı verinin ilişkisel olduğu ana veriyi güncelleme

```csharp
using (var context = new YourDbContext())
{
    // Güncellenecek adresi seç
    var address = context.PersonAddresses.Include(a => a.Person).FirstOrDefault(a => a.PersonAddressId == 1);

    if (address != null)
    {
        // Dependent tarafı güncelle
        address.Street = "New Street";
        address.City = "New City";

        // Bağlı olan principal tarafı güncelle
        address.Person.Name = "New Person Name";

        // Değişiklikleri kaydet
        context.SaveChanges();
    }
}
```
### One to Many İlişkisel Senaryolarda Veri Güncelleme
- Blog (Principal)
- Posts (Dependent)
#### 1. Durum | Esas tablodaki veriye bağımlı verileri değiştirme

```csharp
Blog? blog = await context.Blogs
    .FirstOrDefaultAsync(b => b.Id == 1);

post.Name = "New Post";
post.Blog = _context.Blogs.Find(2);

await context.SaveChangesAsync();
```
#### 2. Durum | Bağımlı verilerin ilişkisel olduğu ana veriyi güncelleme

```csharp
// Var olmayan bir blog'u oluşturup güncelleme.
Post? post = await context.Posts.FindAsync(4);
post.Blog = new()
{
    Name = "2. Blog"
};
await context.SaveChangesAsync();

// Var olan bir blog'la güncelleme.
Post? post = await context.Posts.FindAsync(5);
Blog? blog = await context.Blogs.FindAsync(2);
post.Blog = blog;
await context.SaveChangesAsync();
```
### Many to Many İlişkisel Senaryolarda Veri Güncelleme
#### 1. Örnek

```csharp
Book? book = await context.Books.FindAsync(1);
Author? author = await context.Authors.FindAsync(3);
book.Authors.Add(author);

await context.SaveChangesAsync();
```
#### 2. Örnek

```csharp
Author? author = await context.Authors
    .Include(a => a.Books)
    .FirstOrDefaultAsync(a => a.Id == 3);

foreach (var book in author.Books)
{
    if (book.Id != 1)
        author.Books.Remove(book);
}

await context.SaveChangesAsync();
```
#### 3. Örnek

```csharp
Book? book = await context.Books
    .Include(b => b.Authors)
    .FirstOrDefaultAsync(b => b.Id == 2);

Author silinecekYazar = book.Authors.FirstOrDefault(a => a.Id == 1);
book.Authors.Remove(silinecekYazar);

Author author = await context.Authors.FindAsync(3);
book.Authors.Add(author);
book.Authors.Add(new() { AuthorName = "4. Yazar" });

await context.SaveChangesAsync();
```
