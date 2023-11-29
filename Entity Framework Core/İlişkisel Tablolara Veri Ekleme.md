# İlişkisel Tablolara Veri Ekleme

### One to One İlişkisel Senaryolarda Veri Ekleme

Eğer ki principal entity üzerinden ekleme gerçekleştiriliyorsa dependent entity nesnesi verilmek zorunda değildir! Amma velakin, dependent entity üzerinden ekleme işlemi gerçekleştiriliyorsa eğer burada principal entitynin nesnesine ihtiyacımız zaruridir.
#### 1. Yöntem -> Principal Entity Üzerinden Dependent Entity Verisi Ekleme

```csharp
Person person = new();
person.Name = "Hüseyin";
person.Address = new() { PersonAddress = "Etimesgut/ANKARA" };

await context.AddAsync(person);
await context.SaveChangesAsync();
```
#### 2. Yöntem -> Dependent Entity Üzerinden Principal Entity Verisi Ekleme

```csharp
Address address = new()
                  {
                      PersonAddress = "Papaz Deresi/Ankara",
                      Person = new() { Name = "Şuayip" }
                  };

await context.AddAsync(address);
await context.SaveChangesAsync();
```
### One to Many İlişkisel Senaryolarda Veri Ekleme
#### 1. Yöntem -> Principal Entity Üzerinden Dependent Entity Verisi Ekleme
- **Nesne Referansı Üzerinden Ekleme**: Verilerin eklenebilmesi için Principal Entity'nin kurucusunda Dependent Entity Verilerini tutan koleksiyonun başlatılması gerekir.
```csharp
class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }
    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts { get; set; }
}
```

```csharp
Blog blog = new() { Name = "Gencayyildiz.com Blog" };
blog.Posts.Add(new() { Title = "Post 1" });
blog.Posts.Add(new() { Title = "Post 2" });
blog.Posts.Add(new() { Title = "Post 3" });

await context.AddAsync(blog);
await context.SaveChangesAsync();
```
 - **Object Initializer Üzerinden Ekleme**

```csharp
Blog blog2 = new()
{
    Name = "A Blog",
    Posts = new HashSet<Post>() { new() { Title = "Post 4" }, new() { Title = "Post 5" } }
};

await context.AddAsync(blog2);
await context.SaveChangesAsync();
```
**2. Yöntem -> Dependent Entity Üzerinden Principal Entity Verisi Ekleme**

```csharp
Post post = new()
{
    Title = "Post 6",
    Blog = new() { Name = "B Blog" }
};

await context.AddAsync(post);
await context.SaveChangesAsync();
```
**3. Yöntem -> Foreign Key Kolonu Üzerinden Veri Ekleme**

1 ve 2. yöntemler hiç olmayan verilerin ilişkisel olarak eklenmesini sağlarken, bu 3. yöntem önceden eklenmiş olan bir pricipal entity verisiyle yeni dependent entitylerin ilişkisel olarak eşleştirilmesini sağlamaktadır.
```csharp
Post post = new()
            {
                BlogId = 1,
                Title = "Post 7"
            };

await context.AddAsync(post);
await context.SaveChangesAsync();
```
### Many to Many İlişkisel Senaryolarda Veri Ekleme

#### 1. Yöntem
n to n ilişkisi eğer ki default convention üzerinden tasarlanmışsa kullanılan bir yöntemdir. Her iki Entity için de geçerlidir.
```csharp
// Yeni bir nesne de oluşturulabilir veya var olan bir nesneyi linq sorgusu ile elde ettikten sonra Authors koleksiyonuna Add() metodu ile ekleme yapılır.
Book book = new()
            {
                BookName = "A Kitabı",
                Authors = new HashSet<Author>()
						  {
							  // Var olmayan yazarı oluşturup ekleme.
						      new(){ AuthorName = "Hilmi" },
						      new(){ AuthorName = "Ayşe" },
						      new(){ AuthorName = "Fatma" },
						  }
            };
// Var olan yazarı ekleme.
book.Authors.Add(author);

await context.Books.AddAsync(book);
await context.SaveChangesAsync();
```
#### 2. Yöntem
n to n ilişkisi eğer ki fluent api ile tasarlanmışsa kullanılan bir yöntemdir.
Cross table:
```csharp
class AuthorBook
{
    public int BookId { get; set; }
    public int AuthorId { get; set; }
    public Book Book { get; set; }
    public Author Author { get; set; }
}
```

```csharp
Author author = new()
                {
                    AuthorName = "Mustafa",
                    Books = new HashSet<AuthorBook>() 
                    {
		                    // Yazara var olan bir kitabı ekleme.
					        new(){ BookId = 1},
					        // Yazara var olmayan bir kitabı ekleme.
					        new(){ Book = new () { BookName = "B Kitap" } 
					}
			    }
                
await context.AddAsync(author);
await context.SaveChangesAsync();
```
