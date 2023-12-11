# CRUD
  
**CRUD:** bir veritabanında yapılan temel işlemleri temsil eden Create (Oluştur), Read (Oku), Update (Güncelle) ve Delete (Sil) kısaltmalarını ifade eder. CRUD işlemleri, bir uygulamanın veritabanıyla etkileşimde bulunmasının temelidir. Aşağıda basit bir ASP.NET Core uygulamasında CRUD işlemleri için bir örnek bulunmaktadır. Bu örnekte, Entity Framework Core kullanılarak bir veritabanı ile etkileşim sağlanmaktadır.
- **Add:** veritabanına yeni bir varlık (entity) eklemek için kullanılır. Yeni bir varlık nesnesi oluşturulduktan sonra, bu metot ile bu varlık nesnesi DbContext içindeki ilgili DbSet koleksiyonuna eklenir. Ancak, bu değişiklikler henüz veritabanına kaydedilmemiştir; sadece DbContext içinde bir değişiklik birimi oluşturulmuştur.
- **Update:** varlık nesnesini güncellemek için kullanılır. Ancak, EF Core'da Update metodu olmasına rağmen kullanımı zorunlu değildir. Bunun yerine, bir varlık nesnesi okunduktan sonra (veritabanından çekildikten sonra), bu varlık nesnesinde yapılan değişiklikler otomatik olarak algılanır ve SaveChanges metodu çağrıldığında bu değişiklikler veritabanına uygulanır.
- **Remove:** varlık nesnesini veritabanından silmek için kullanılır. Silinecek varlık nesnesi, ilgili DbSet koleksiyonundan kaldırılır ve SaveChanges metodu çağrıldığında bu değişiklikler veritabanına uygulanır.
- **SaveChanges:** Entity Framework Core (EF Core) kullanılarak yapılan değişiklikleri veritabanına kaydetmek için kullanılır. Yani, Add, Update, ve Remove gibi değişiklikleri bir veritabanına uygulamak için bu metot çağrılır.
```csharp
public class ProductController : Controller
{
    private readonly ApplicationDbContext _context;

    public ProductController(ApplicationDbContext context)
    {
        _context = context;
    }
```

**CREATE:**
```csharp
    public IActionResult Create()
    {
        return View();
    }

    [HttpPost]
    public IActionResult Create(Product product)
    {
        if (ModelState.IsValid)
        {
            await _context.Products.AddAsync(product);
            await _context.SaveChangesAsync();
            return RedirectToAction("Index");
        }
        return View(product);
    }
```
**READ:**
```csharp
    public IActionResult Index()
    {
        var products = await _context.Products.ToListAsync();
        return View(products);
    }
```
**UPDATE:**
```csharp
    public IActionResult Edit(int? id)
    {
        if (id is not null)
        {
	        var product = await _context.Products.FindAsync(id);
	        if (product == null)
		        return NotFound();
		    
	        return View(product);
		}
		return NotFound();
    }

    [HttpPost]
    public IActionResult Edit(Product product)
    {
        if (ModelState.IsValid)
        {
            await _context.Products.UpdateAsync(product); // Olmasa da olur.
            await _context.SaveChangesAsync();
            return RedirectToAction("Index");
        }

        return View(product);
    }
```
**DELETE:**
```csharp
    [HttpPost]
    public IActionResult Delete(int id)
    {
        var product = await _context.Products.FindAsync(id);
        await _context.Products.RemoveAsync(product);
        await _context.SaveChangesAsync();
        return RedirectToAction("Index");
    }
}
```
**Range Fonksiyonu:**
-  Belirli bir aralıktaki verilere erişmek, sorgulamak veya güncellemek için kullanışlıdır.
```csharp
using (var context = new YourDbContext())
{
    var newProducts = new List<Product>
    {
        new Product { ProductName = "Product1", Price = 10.99 },
        new Product { ProductName = "Product2", Price = 15.99 },
        new Product { ProductName = "Product3", Price = 20.99 }
    };

    await context.Products.AddRangeAsync(newProducts);
    await context.SaveChangesAsync();
}
```
```csharp
using (var context = new YourDbContext())
{
    var productsToDelete = await context.Products
        .Where(p => p.Price >= 30)
        .ToListAsync();

    await context.Products.RemoveRangeAsync(productsToDelete);
    await context.SaveChangesAsync();
}
```
