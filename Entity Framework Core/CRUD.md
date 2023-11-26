# CRUD
  
**CRUD:** bir veritabanında yapılan temel işlemleri temsil eden Create (Oluştur), Read (Oku), Update (Güncelle) ve Delete (Sil) kısaltmalarını ifade eder. CRUD işlemleri, bir uygulamanın veritabanıyla etkileşimde bulunmasının temelidir. Aşağıda basit bir ASP.NET Core uygulamasında CRUD işlemleri için bir örnek bulunmaktadır. Bu örnekte, Entity Framework Core kullanılarak bir veritabanı ile etkileşim sağlanmaktadır.
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
            _context.Products.Add(product);
            _context.SaveChanges();
            return RedirectToAction("Index");
        }
        return View(product);
    }
```
**READ:**
```csharp
    public IActionResult Index()
    {
        var products = _context.Products.ToList();
        return View(products);
    }
```
**UPDATE:**
```csharp
    public IActionResult Edit(int? id)
    {
        if (id is not null)
        {
	        var product = _context.Products.Find(id);
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
            _context.Products.Update(product);
            _context.SaveChanges();
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
        var product = _context.Products.Find(id);
        _context.Products.Remove(product);
        _context.SaveChanges();
        return RedirectToAction("Index");
    }
}
```
