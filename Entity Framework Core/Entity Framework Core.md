
# Entity Framework Core
**Entity Framework Core (EF Core):** bir nesne ilişkilendirme (Object-Relational Mapping - ORM) çerçevesidir ve .NET uygulamalarında veri tabanı etkileşimlerini yönetmeyi sağlar.
**Kurulması gereken paketler(Nuget Packages):**

- Microsoft.EntityFramework.Core 
 -  Microsoft.EntityFramework.Core.Tools ---> *PMC* 
 -  Microsoft.EntityFramework.Core.Design ---> *DotNet CLI* 
 - Microsoft.EntityFramework.Core.[Provider] 
 [Provider] *kısmına projede kullanılacak veritabanının ismi yazılmalıdır.*
 **Provider'ın Connection Stringini öğrenmek için:** https://www.connectionstrings.com

1. **Model (Model):**
   Model, uygulamanızın veri tabanındaki nesneleri temsil eden sınıfları içerir. Bu sınıflar genellikle veri tabanı tablolarını, ilişkilerini ve diğer yapıları yansıtır. Model sınıfları, genellikle veri tabanında bir **`tablo`**'yu temsil eder ve **`tekil`** olarak isimlendirilirler. Modellerin her bir property'si de veritabanındaki tabloda **`Sütun`**'a denk gelir. **`Entities`** dizininin içinde oluşturulurlar.

   ```csharp
   public class Product
   {
       public int Id { get; set; }
       public string Name { get; set; }
       public decimal Price { get; set; }
   }
   ```

2. **DbContext:**
   DbContext, uygulamanın veri tabanı ile etkileşimini yöneten ana sınıftır. Bu sınıf, model sınıflarının bir koleksiyonunu içerir ve veri tabanı bağlantısını yönetir. DbContext, veri tabanındaki tabloları ve ilişkileri tanımlar. **`Context`** dizininin içinde oluşturulur.

   ```csharp
   public class ApplicationDbContext : DbContext
   {
       public DbSet<Product> Products { get; set; }
   }
   ```

3. **DbSet:**
   DbSet, DbContext içinde bulunan bir koleksiyonu temsil eder. Her DbSet öğesi, veri tabanındaki bir **`tablo`**'ya karşılık gelir ve ilgili model sınıfını içerir. **`Çoğul`** olarak isimlendirilirler.

   ```csharp
   public DbSet<Product> Products { get; set; }
   ```
4. **Provider:**
   EF Core, farklı veri tabanlarına bağlantı sağlayan "provider"ları destekler. Veri tabanına özgü provider'lar, ilgili veri tabanı ile etkileşimde bulunmak için gereken bağlantı ve sorgu stratejilerini içerir.

   ```csharp
   services.AddDbContext<ApplicationDbContext>(options =>
       options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
   ```


Bu bileşenler bir araya geldiğinde, EF Core, .NET uygulamanızda veri tabanı işlemlerini kolaylaştıran bir ORM çerçevesi sağlar. Model sınıfları, DbContext ve Migration'lar aracılığıyla veri tabanı işlemlerini yönetebilirsiniz.

