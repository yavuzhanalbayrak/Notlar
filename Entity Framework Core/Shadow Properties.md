# Shadow Properties
Entity Framework Core'da "shadow properties" (gölge özellikler), veritabanındaki bir sütunu model sınıfında belirtmeden, sadece veritabanındaki bir özelliği temsil etmek için kullanılan özelliklerdir. Bu özellikler, veritabanı şemasında bir alanı temsil eder, ancak model sınıfında herhangi bir şema tanımlamazlar.

Tıpkı **`One to Many`** ve **`Many to Many`** ilişkilerindeki **`Foreign Key`**'in Entity'de tanımlanmamasına rağmen EF Core tarafından Veritabanında oluşturulup yaptığımız sorgularda kullanılıyor olması gibi.

Örneğin, bir `Blog` sınıfı ve bu sınıfa ait bir `CreatedAt` alanı olduğunu düşünün. Ancak, bu `CreatedAt` alanını model sınıfında doğrudan tanımlamak istemiyorsunuz. Bunun yerine, bu özelliği bir "gölge özellik" olarak ekleyebilirsiniz:

```csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Title { get; set; }
    
    // Gölge özellik: CreatedAt veritabanında bir sütunu temsil eder
    public DateTime CreatedAt { get; set; }
}
```

Bu durumda, `CreatedAt` özelliği, model sınıfında belirtilmemiş olsa da, Entity Framework Core tarafından veritabanında bir sütunu temsil etmek için kullanılır. Bu özelliğin veritabanındaki sütunu temsil ettiği ve veritabanında ilgili bir sütun varsa, bu sütun üzerinde işlemler gerçekleştirilebilir.

Bu yaklaşım, genellikle bir sınıfın bazı özelliklerini veritabanına eklemek istenirken, bu özelliklerin model sınıfında açıkça görünmemesi gerektiği durumlar için kullanılır. Shadow properties, Entity Framework Core'un daha fazla esneklik sağlamasına ve model sınıflarının veritabanı şemasını bilmemesine olanak tanır.
### Kullanımı:
Shadow properties, Entity Framework Core'da, `ModelBuilder` sınıfının `Entity<TEntity>()` metodu aracılığıyla eklenir. `Entity<TEntity>()` metodu, bir varlık türünü temsil eden `EntityTypeBuilder<TEntity>` nesnesini döndürür ve bu nesne üzerinden shadow properties ekleyebilirsiniz.

İşte bir örnek:

```csharp
using Microsoft.EntityFrameworkCore;

public class BlogContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
	protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
	{ 		
		optionsBuilder.UseSqlServer("your_connection_string_here"); 
	}
	
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property<DateTime>("CreatedAt");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Title { get; set; }
    // CreatedAt, model sınıfında açıkça belirtilmemiş, ancak shadow property olarak eklenmiştir
}
```

Bu örnekte, `Blog` sınıfında `CreatedAt` adında bir shadow property eklenmiştir. `OnModelCreating` metodunda, `Entity<Blog>()` ile `EntityTypeBuilder<Blog>` nesnesi alınır ve `Property<DateTime>("CreatedAt")` ile `CreatedAt` adında bir shadow property eklenir.

Bu shadow property'ye veri eklemek veya bu veriyi çekmek içinse şu şekilde kullanılabilir:

```csharp
using System;

class Program
{
    static void Main()
    {
        using (var context = new BlogContext())
        {
            var blog = new Blog
            {
                Title = "Sample Blog"
            };

            // Shadow property'e değer atama
            context.Entry(blog).Property("CreatedAt").CurrentValue = DateTime.Now;

            // Shadow property'den değer çekme
            var createdAtValue = context.Entry(blog).Property("CreatedAt").CurrentValue;
            Console.WriteLine($"CreatedAt: {createdAtValue}");

	    // LINQ sorgusu kullanarak shadow property ile sorgu
               var recentBlogs = await context.Blogs
					.Where(b => EF.Property<DateTime>(b, "CreatedAt") > DateTime.Now.AddMonths(-1)) 
					.ToListAsync();

            await context.Blogs.AddAsync(blog);
            await context.SaveChangesAsync();
        }
    }
}
```

Bu örnekte, `Entry(blog)` ile bir varlığın `EntityEntry`'sine ulaşılır ve `Property("CreatedAt")` ile shadow property'ye erişilir. `CurrentValue` özelliği ile shadow property'nin değeri okunabilir veya değiştirilebilir. Bu shadow property, `DateTime` türünde olduğu için `Where` koşulunda `EF.Property<DateTime>(blog, "CreatedAt")` ifadesi ile erişilebilir ve sorgular içinde kullanılabilir. Shadow property'lerin değerleri, ilgili varlık nesnesi veritabanına kaydedilmeden önce veya sonra ayarlanabilir.
