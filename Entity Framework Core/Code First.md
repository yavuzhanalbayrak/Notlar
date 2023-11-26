
# Code First

**Code First:** Entity Framework (EF) ORM (Object-Relational Mapping) çerçevesindeki bir yaklaşımı ifade eder. Code First, uygulama tarafından yazılan sınıflar (code) temelinde veritabanının oluşturulmasını ve yönetilmesini sağlar. Bu yaklaşım, veritabanının şema (schema) oluşturmasına yönelik kod tarafında yapılan tanımlamalara dayanır.

Code First yaklaşımı şu temel adımları içerir:

1. **Sınıfların Tanımlanması:** Uygulama geliştiricisi, uygulama mantığına uygun **`Entity`**'leri tanımlar. Bu sınıflar genellikle uygulama içindeki nesneleri temsil eder ve veritabanındaki tabloların karşılıklarını oluşturur.

   ```csharp
   public class Product
   {
       public int ProductId { get; set; }
       public string Name { get; set; }
       public decimal Price { get; set; }
   }
   ```

2. **DbContext Sınıfının Oluşturulması:** DbContext, Code First yaklaşımında önemli bir sınıftır. Bu sınıf, uygulama tarafından tanımlanan sınıfları ve bu sınıflar arasındaki ilişkileri kullanarak veritabanıyla etkileşimi sağlar.

   ```csharp
   public class ApplicationDbContext : DbContext
   {
       public DbSet<Product> Products { get; set; }
   }
   ```

3. **Migration'ların Kullanılması:** Migration'lar, Code First yaklaşımında veritabanındaki değişiklikleri yönetmek için kullanılır. Migration'lar, uygulama tarafındaki model değişikliklerini veritabanına uygulamak veya geri almak için kullanılır.

	 **Package Manager Console(PMC):**
   
   *Migration Oluşturma:*
   ```bash
   add-migration [Migration Name]
   ```
   *Migration Path Belirleme:*
   ```bash
   add-migration [Migration Name] -OutputDir [Path]
   ```
   *Migration Silme:*
   ```bash
   remove- migration
   ```
   *Migration Listeleme:*
   ```bash
   get-migration
   ```
   *Migrate Etme:*
   ```bash
   update-database
   ````
   *Migration Geri Alma:*
   ```bash
   update-database [Migration Name]
   ```
   *Kod Üzerinden Migrate Operasyonu:*
   ```csharp
   AppDbContext context = new();
   await context.Database MigrateAsync();
   ```
   **Dotnet CLI:**
   ```bash
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ...
   ```

 
   Yukarıdaki komutlarla yeni bir migration oluşturulur ve veritabanına güncellenir.
4. `OnConfiguring` metodu, Entity Framework Core'un `DbContext` sınıfında yer alan bir metoddur. Bu metot, veritabanına bağlantı sağlamak için gerekli olan konfigürasyonu yapmanıza olanak tanır. Genellikle, `DbContext` sınıfının türetilen sınıfında bu metodu kullanarak veritabanına nasıl bağlanılacağınızı belirtirsiniz.

	Aşağıda tipik bir `OnConfiguring` metodu örneği bulunmaktadır:
	```csharp
	public  class  ApplicationDbContext : DbContext 
	{ 
		public DbSet<Product> Products { get; set; } 
		protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
		{ 
			if (!optionsBuilder.IsConfigured) 
			{ 
				optionsBuilder.UseSqlServer("your_connection_string"); 
			} 
		} 
	}
	```

	Bu örnekte:

- `DbContextOptionsBuilder`: Bu sınıf, veritabanına bağlanma seçeneklerini sağlar. `OnConfiguring` metoduna geçilen `optionsBuilder` parametresi üzerinden bu seçenekleri belirleyebilirsiniz.

- `IsConfigured`: Bu, bağlantı yapılandırmasının zaten yapılıp yapılmadığını kontrol eden bir özelliktir. Bu sayede, her defasında bağlantıyı yapılandırmaktan kaçınılabilir.

- `UseSqlServer`: Bu metot, SQL Server veritabanına bağlanmak için kullanılır. Bağlantı dizesi (connection string) olarak `"your_connection_string"` ifadesini içermelidir. Provider'ın Connection Stringini öğrenmek için `https://www.connectionstrings.com` adresinden bakabilirsiniz.

	`OnConfiguring` metodunu kullanmak, genellikle bağlantı dizesi ve sağlayıcı (provider) belirleme gibi işlemleri içerir. Ancak, dikkat edilmesi gereken bir nokta, bu bilgilerin kod içinde açık bir şekilde bulunmasının güvenlik riski oluşturabileceğidir. Bu nedenle, gerçek uygulamalarda bu tür hassas bilgilerin güvenli bir şekilde saklanması **`appsettings`** önerilir, örneğin kullanıcılar arasında paylaşılan bir yapılandırma dosyasında veya bir güvenlik servisinden alınarak.


Bu yaklaşım, özellikle uygulama kodunu temel alarak veritabanı tasarımını ve şemasını esnek bir şekilde kontrol etmek istediğiniz.
**Örnek:**
Context sınıfı:
```csharp
public  class  AppDbContext : DbContext 
{ 
	public DbSet<User> Users { get; set; } 
	protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
	{ 
		optionsBuilder.UseSqlServer("Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=SagoistSite2;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False");
	} 
}
```
Entity sınıfı:
```csharp
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Surname { get; set; }
    public string Email { get; set; }
    public string Password { get; set; }
}
```
Default Primary Key:
```csharp
public int Id { get; set; }
public int ID { get; set; }
public int UserId { get; set; }
public int UserId { get; set; }
```
Custom Primary Key:
```csharp
[Key]
public int custom_id { get; set; }
```

