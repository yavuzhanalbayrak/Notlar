# Kullanıcıdan Veri Alma Yöntemleri


ASP.NET Core MVC 6.0'da kullanıcıdan veri almanın çeşitli yolları vardır. Kullanıcıdan gelen veriler genellikle HTTP istekleri üzerinden alınır ve bu verilerin işlenmesi için farklı yöntemler kullanılabilir. İşte bazı yaygın yöntemler:

1. **Form Verileri Almak:**
   Kullanıcıdan veri almanın en yaygın yollarından biri HTML formalarını kullanmaktır. Form elemanları, kullanıcıdan alınan verileri sunucuya iletmek için kullanılır. Controller sınıfındaki bir aksiyon metodu, bu form verilerini parametre olarak alabilir.

   ```csharp
   [HttpPost]
   public IActionResult FormVerileriniAl(string ad, string soyad)
   {
       // ad ve soyad parametreleri, formdan gelen verileri temsil eder
       // İşlemler...
       return View();
   }
   ```

   Bu örnekte, `FormVerileriniAl` aksiyon metodu, HTTP POST isteği ile çağrıldığında, formdan gelen "ad" ve "soyad" parametrelerini alır.

2. **Query String ile Veri Almak:**
   Kullanıcıdan veri almanın başka bir yolu da query string (sorgu dizisi) kullanmaktır. Query string, URL'nin sonunda yer alan `?anahtar=değer` şeklindeki veri parçacıklarıdır.

   ```csharp
   public IActionResult QueryStringVerisiAl(string ad)
   {
       // ad parametresi, query string'den gelen veriyi temsil eder
       // İşlemler...
       return View();
   }
   ```

   Bu örnekte, `QueryStringVerisiAl` aksiyon metodu, query string'den gelen "ad" parametresini alır.

3. **Route Parametreleri ile Veri Almak:**
   MVC uygulamalarında, route parametreleri kullanarak URL'den veri almak mümkündür. Route parametreleri, URL'nin bir parçası olarak taşınan değerlerdir.

   ```csharp
   app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id:int?}");
     ```
     ```csharp
   public IActionResult RouteParametresiAl(int id)
   {
       // id parametresi, URL'den gelen değeri temsil eder
       // İşlemler...
       return View();
   }
   ```

   Bu örnekte, `RouteParametresiAl` aksiyon metodu, URL'lerdeki `{id}` route parametresini alır.

4. **Model Binding Kullanmak:**
   ASP.NET Core MVC, model binding özelliği sayesinde HTTP isteği içindeki verileri doğrudan bir model sınıfına bağlama imkanı sunar.

   ```csharp
   [HttpPost]
   public IActionResult ModelBindKullan(UserViewModel model)
   {
       // model parametresi, HTTP isteği içindeki verileri temsil eder
       // İşlemler...
       return View();
   }
   ```

   Bu örnekte, `ModelBindKullan` aksiyon metodu, HTTP POST isteği ile gönderilen verileri `UserViewModel` sınıfına bağlar.

Bu yöntemlerden hangisini kullanacağınız, uygulamanızın gereksinimlerine bağlıdır. Genellikle HTML form kullanımı ve model binding, kullanıcıdan veri almak için sıkça kullanılan yöntemlerdir.
