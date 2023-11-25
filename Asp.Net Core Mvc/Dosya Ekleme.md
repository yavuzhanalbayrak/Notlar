# Dosya Ekleme


Formdan gelen bir dosyanın (örneğin bir resim dosyasının) sunucuda belirli bir klasöre kaydedilmesi için aşağıda basit bir örnek verilmiştir. Bu örnekte `Image` klasörüne bir resim dosyasının kaydedilmesi işlemi gerçekleştirilmektedir.

Öncelikle, bir `UploadViewModel` oluşturalım:

```csharp
public class UploadViewModel
{
    public IFormFile File { get; set; }
}
```

Şimdi, bu modeli kullanarak bir Controller oluşturalım:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using System;
using System.IO;

public class UploadController : Controller
{
    [HttpGet]
    public IActionResult Index()
    {
        return View();
    }

    [HttpPost]
    public async Task<IActionResult> Upload(UploadViewModel model)
    {
        if (model.File != null && model.File.Length > 0)
        {
            // Uzantıyı al (örneğin ".jpg")
            var fileExtension = Path.GetExtension(model.File.FileName);

            // Özgün bir dosya adı oluştur (guid + uzantı)
            var filename = $"{Guid.NewGuid()}{fileExtension}";

            // Dosyanın kaydedileceği tam yolu oluştur
            var path = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot", "images", filename);

            // Dosyayı belirlenen yola kaydet
            using (var stream = new FileStream(path, FileMode.Create))
            {
                await model.File.CopyToAsync(stream);
            }

            // Başarılı bir mesaj döndür (veya başka bir işlem gerçekleştir)
            ViewBag.Message = "Dosya başarıyla yüklendi!";
            return View("Index");
        }

        // Dosya yüklenmezse hata mesajı göster
        ViewBag.Error = "Geçerli bir dosya seçin.";
        return View("Index");
    }
}
```
