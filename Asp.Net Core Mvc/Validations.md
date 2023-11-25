# Validations
**Validation:**  Genellikle bilgi veya veri girişlerinin belirli kriterlere uygun olup olmadığını kontrol etme sürecidir. Bu süreç, verilerin belli bir formata, sınıra veya kuralla uyup uymadığını değerlendirerek doğruluğunu sağlar. Validation işlemi, genellikle kullanıcı girişi, form verileri veya dış kaynaklardan alınan verilerin geçerliliğini kontrol etme amacıyla kullanılır.

Validation'ın ana hedefleri şunlardır:

1.  **Veri bütünlüğünü sağlamak:** Veritabanlarına veya başka veri depolama yerlerine kaydedilen verilerin belirli bir formata veya kurallara uygun olmasını sağlamak.
    
2.  **Güvenlik:** Kötü niyetli kullanıcıların veya hatalı girişlerin neden olduğu güvenlik açıklarını önlemek. Örneğin, SQL enjeksiyonu veya form doğrulama saldırıları gibi güvenlik sorunlarına karşı koruma sağlamak.
    
3.  **Kullanıcı Arayüzü Deneyimini İyileştirmek:** Kullanıcıya, girişlerini düzeltmesi veya eksik bilgileri tamamlaması konusunda geri bildirimde bulunarak daha kullanıcı dostu bir deneyim sağlamak.
    

Validation, çeşitli kuralları ve teknikleri içerebilir. Örneğin:

-   **Zorunluluk Kontrolü (Required Validation):** Belirli bir alanın doldurulmuş olması gerektiğini kontrol etme.
    
-   **Uzunluk Kontrolü (Length Validation):** Bir alanın belirli bir uzunlukta olup olmadığını kontrol etme.
    
-   **Format Kontrolü (Format Validation):** Bir alanın belirli bir formata uygun olup olmadığını kontrol etme, örneğin, bir e-posta adresinin geçerli bir formata sahip olup olmadığını kontrol etme.
    
-   **Sayı Aralığı Kontrolü (Range Validation):** Bir sayının belirli bir aralıkta olup olmadığını kontrol etme.
    
-   **Benzersizlik Kontrolü (Uniqueness Validation):** Bir değerin veritabanında benzersiz olup olmadığını kontrol etme.
    

Validation, genellikle hem sunucu tarafında (server-side) hem de istemci tarafında (client-side) gerçekleştirilir. Sunucu tarafındaki doğrulama, genellikle güvenilir ve zorunludur çünkü kullanıcılar tarafından gönderilen verilere güvenilmemelidir. Ancak, istemci tarafındaki doğrulama, kullanıcı arayüzünde hızlı geri bildirim sağlayabilir ve kullanıcı deneyimini iyileştirebilir, ancak güvenilir bir kaynak olarak kabul edilmemelidir çünkü kullanıcı tarafından değiştirilebilir.

**Örneğin;**
```csharp
public class Ogrenci
{
    [Display(Name = "Öğrenci No")]
    public string OgrNo { get; set; }
    [Required(ErrorMessage = "Ad alanı zorunlu")]
    [Display(Name = "Öğrenci Ad")]
    public string OgrAd { get; set; }
    [Display(Name = "Öğrenci Soyad")]
    public string OgrSoyad { get; set; }
}
```
**Modelin sunucu taraflı doğrulanması:**
```csharp
 [HttpPost]
   public IActionResult Create(Ogrenci ogr)
   {
       if (!ModelState.IsValid)
       {
           return View(customer);
       }
       
       // ... işlemi gerçekleştir ...
       
       return RedirectToAction("Index");
   }
``` 
**Modelin istemci taraflı doğrulanması:**
```html
@model Ogrenci
<!DOCTYPE html>
<html style="font-size: 16px;" lang="tr">
<head>
	@*Client-side validation için eklenmesi gereken kütüphaneler: *@
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/jquery-validation/dist/jquery.validate.min.js"></script>
    <script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.min.js"></script>
</head>
<body>
	@*asp-validation-for ile hata mesajı yayınlanır.*@
	<form asp-controller="Ogrenci" asp-action="Ekle" method="post" enctype="multipart/form-data">
		<label for="OgrAd" class="form-label"><h5></h5></label>
		<input asp-for="OgrAd" type="text" class="form-control" aria-describedby="emailHelp">
		<span asp-validation-for="OgrAd"></span>
		<button type="submit" >Submit</button>
		@*...*@
	</form>
</body>
</html>
```
## Model Metadata Types

*ASP.NET MVC'de model meta veri türleri, model sınıfınızın niteliklerini (property) açıklamak ve model üzerinde uygulanan doğrulama (validation) kurallarını tanımlamak için kullanılır. Bu tür meta veri türleri, kullanıcı arayüzlerinde hata mesajları göstermek ve kullanıcının doğru veri girişi yapmasını sağlamak gibi işlevleri destekler.*

*Bu tür meta veri türleri genellikle `System.ComponentModel.DataAnnotations` ad alanı altında yer alır ve `MetadataType` özniteliği kullanılarak belirtilir. Aşağıda basit bir örnek bulunmaktadır:*


```csharp
using System;
using System.ComponentModel.DataAnnotations;

// Ana model sınıfı
public class UserModel
{
    public int UserId { get; set; }

    public string UserName { get; set; }

    public int Age { get; set; }
}
```
```csharp
// Model meta veri sınıfı
[MetadataType(typeof(UserModelMetaData))]
public class UserModelMetaData
{
    // UserName özelliği için doğrulama kuralları
    [Required(ErrorMessage = "Kullanıcı adı boş bırakılamaz.")]
    [StringLength(50, ErrorMessage = "Kullanıcı adı 50 karakterden uzun olamaz.")]
    public string UserName { get; set; }

    // Age özelliği için doğrulama kuralları
    [Range(18, 99, ErrorMessage = "Yaş 18 ile 99 arasında olmalıdır.")]
    public int Age { get; set; }
}
```

Bu örnekte, `UserModel` sınıfındaki `UserName` ve `Age` özellikleri için doğrulama kuralları `UserModelMetaData` sınıfında belirtilmiştir. Bu sayede, ASP.NET MVC, bu kuralları kullanarak kullanıcıdan alınan verilerin doğruluğunu değerlendirebilir ve hata mesajları üretebilir.

Bu yaklaşım, model sınıflarınızı daha düzenli ve temiz tutmanıza yardımcı olabilir. Ayrıca, bu tür bir ayrım, bir modelin birden çok görünümde (view) kullanılması durumunda da faydalı olabilir, çünkü her görünüm için farklı doğrulama kuralları belirtilebilir.

## Fluent Validation
1.  **FluentValidation Paketini Ekleyin**: NuGet paket yöneticisi aracılığıyla projenize FluentValidation ve FluentValidation.AspNetCore paketlerini ekleyin.
    
    `Install-Package FluentValidation.AspNetCore`
    

2.  **Doğrulayıcı Sınıfı Oluşturma**: Diyelim ki bir `Customer` modeliniz var:

 ```csharp
 public class Customer
   {
       public string Name { get; set; }
       public string Email { get; set; }
       // ... diğer özellikler ...
   }
``` 

Bu model için bir doğrulayıcı sınıf oluşturun:

 ```csharp
 using FluentValidation;

   public class CustomerValidator : AbstractValidator<Customer>
   {
       public CustomerValidator()
       {
           RuleFor(customer => customer.Name).NotEmpty();
           RuleFor(customer => customer.Email).EmailAddress();
           // ... diğer kurallar ...
       }
   }
```

3.  **ASP.NET Core 6.0 MVC'de FluentValidation Kullanma**:`Program.cs` dosyasında FluentValidation'ı hizmetlere ekleyin.

 ```csharp
 // ...
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllersWithViews().AddFluentValidation(fv =>
{
    fv.RegisterValidatorsFromAssemblyContaining<Customer>();
});
;
var app = builder.Build();

   // ...
``` 

4.  **Doğrulamanın Kullanılması**: MVC kontrolörlerinizde, model doğrulaması otomatik olarak çalıştırılır. Örneğin:

 ```csharp
 [HttpPost]
   public IActionResult Create(Customer customer)
   {
       if (!ModelState.IsValid)
       {
           return View(customer);
       }
       
       // ... işlemi gerçekleştir ...
       
       return RedirectToAction("Index");
   }
```

