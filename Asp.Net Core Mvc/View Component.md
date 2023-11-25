# View Component
ASP.NET Core'da `ViewComponent`, bir Razor sayfasının veya bir Razor görünümünün içinde tekrar kullanılabilir ve bağımsız bir bileşen olarak davranan bir yapıdır. Bu, bir sayfanın veya görünümün içindeki belirli bir bölümü temsil eden, kendi işlevselliğine ve görünüşüne sahip küçük ve özelleştirilebilir bir bileşen oluşturmanızı sağlar.

`ViewComponent`'lar, genellikle bir Controller ve bir View'a benzer bir yapıda olup, işlevselliğini yürüten C# kodu ve kullanıcı arayüzünü temsil eden Razor görünümlerini içerir. Ancak, Controller'a benzer olarak, bir `ViewComponent`'ın işlemesi için herhangi bir HTTP isteği gerekmez. Bu nedenle, `ViewComponent`'lar, bir Controller'dan farklı olarak, kendi başına çağrılamaz ve başlatılamazlar.

Aşağıda, bir örnek `ViewComponent`'in nasıl oluşturulacağını gösteren basit bir kod örneği bulunmaktadır:

1. **ViewComponent Sınıfı Oluşturma:**
- Genellikle `Model` dizininin içindeki `ViewComponents` dizininde bulunurlar.
   
   ```csharp
   // Örnek bir ViewComponent sınıfı
   public class GreetingViewComponent : ViewComponent
   {
       public IViewComponentResult Invoke(string name)
       {
	       // Gerekli DB vb. işlemler yapılır.
           // Gelen ismi kullanarak özel bir selamlama mesajı oluştur
           var model = $"Merhaba, {name}!";
           return View("Default", model);
       }
   }
   ```

3. **View Oluşturma:**
   
   `Views/Shared/Components/Greeting/Default.cshtml` adında bir Razor görünümü oluşturun:

   ```html
   @model string

   <p>@Model</p>
   ```

4. **Kullanım:**
   
   `Invoke` metodunun çalıştırılacağı bir sayfada veya görünümde, `ViewComponent`'i kullanabilirsiniz:

   ```html
   <!-- Örnek kullanım -->
   <div>
       @await Component.InvokeAsync("Greeting", new { name = "John" })
   </div>
   ```

Bu örnek, `GreetingViewComponent` adlı bir `ViewComponent`'i kullanarak, bir ismi parametre olarak alır ve bu ismi kullanarak bir selamlama mesajı oluşturur. Daha sonra, bu mesajı `Default.cshtml` adlı Razor görünümüyle birleştirir ve sonucu sayfada görüntüler.

`ViewComponent`'lar, genellikle küçük parçalı, tekrar kullanılabilir bileşenler oluşturmak ve bunları sayfalarda veya görünümlerde kullanmak için kullanılır. Bu, bir uygulamanın modülerliğini artırabilir ve karmaşıklığı azaltabilir.
