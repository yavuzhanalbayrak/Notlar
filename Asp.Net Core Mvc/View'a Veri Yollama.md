# View'a Veri Yollama

ASP.NET Core MVC'de bir Controller aksiyonundan bir View'e veri göndermenin birkaç yolu vardır. İşte bu yöntemlerden bazıları:

1. **ViewBag Kullanmak(Dynamic):**
   Controller'daki aksiyon metodundan ViewBag kullanarak View'e veri gönderebilirsiniz. ViewBag, dinamik bir yapıdır ve her türlü veriyi içerebilir.

   ```csharp
   public IActionResult Index()
   {
       ViewBag.Message = "Merhaba, Dünya!";
       return View();
   }
   ```

   View'de bu değeri kullanmak için:

   ```html
   <p>@ViewBag.Message</p>
   ```

2. **ViewData Kullanmak(Object):**
   ViewData da Controller'dan View'e veri göndermenin bir başka yoludur. ViewData, Object bir yapıdır.

   ```csharp
   public IActionResult Index()
   {
       ViewData["Message"] = "Merhaba, Dünya!";
       return View();
   }
   ```

   View'de bu değeri kullanmak için:

   ```html
   <p>@ViewData["Message"]</p>
   ```

3. **Model Kullanmak:**
   Bir diğer yaygın yöntem, Controller aksiyonundan View'e bir model göndermektir. Model, genellikle bir sınıf örneği olarak tanımlanır.

   ```csharp
   public class UserModel
   {
       public string Message { get; set; }
   }

   public IActionResult Index()
   {
       var model = new UserModel
       {
           Message = "Merhaba, Dünya!"
       };

       return View(model);
   }
   ```

   View'de bu değeri kullanmak için:

   ```html
   <p>@Model.Message</p>
   ```

   Bu durumda, View'deki model, Controller'daki aksiyon metodu tarafından belirtilen model sınıfından türetilir.
4. `TempData`, bir Controller aksiyonundan bir View'e geçici veri taşımak için kullanılır. Bu veri, bir HTTP isteği içinde tek bir seferlik olarak taşınır ve bir sonraki istek sırasında kullanıldığında silinir. Genellikle bir View'e yönlendirme (Redirect) işlemleri sırasında kullanılır.

	İşte TempData kullanımı:

	**TempData'ya Veri Eklemek:**
   ```csharp
   public IActionResult Index()
   {
       TempData["Message"] = "Bu bir TempData mesajıdır.";
       return RedirectToAction("DisplayMessage");
   }
   ```

   Bu örnekte, `TempData["Message"]` kullanılarak TempData'ye bir değer eklenir ve "DisplayMessage" aksiyonuna yönlendirme yapılır.

	**TempData'yi View'de Kullanmak:**
   ```csharp
   public IActionResult DisplayMessage()
   {
       ViewBag.Message = TempData["Message"];
       return View();
   }
   ```

   TempData'den değeri aldıktan sonra, ViewBag veya ViewData gibi diğer yöntemlerle View'e geçirebilirsiniz. Bu değer, bir kez kullanıldıktan sonra silinir.
   
   **Tempdata ile komplex bir model yakalama:**
   ```csharp
   public IActionResult DisplayMessage()
   {
	   var products = JsonSerializer.Deserialize<List<Product>>(data);
       return View();
   }
   ```

	**View'de ViewBag ile TempData'yı Gösterme:**
   ```html
   <p>@ViewBag.Message</p>
   ```

   Bu örnekte, TempData'nin içindeki değeri ViewBag aracılığıyla View'de kullanabilirsiniz.

	Bu şekilde TempData, bir Controller aksiyonundan başka bir aksiyona geçici veri taşımak ve kullanmak için kullanılır. Özellikle bir aksiyonun sonucunda bir yönlendirme yapıldığında TempData kullanılır, böylece bir sonraki istek sırasında bu geçici veri kullanılabilir.

Bu yöntemler arasında seçim yaparken, veri taşıma ihtiyacınıza ve uygulamanızın gereksinimlerine göre uygun olanı seçebilirsiniz. Genellikle, model kullanmak, güçlü bir tip güvenliği sağlar ve kodunuzu daha sürdürülebilir hale getirebilir.
