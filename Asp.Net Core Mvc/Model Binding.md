# Model Binding
**Model Binding:** *Model binding, bir web uygulamasında kullanıcının girişlerini (HTTP isteği içindeki verileri) bir model nesnesine otomatik olarak bağlamak veya eşleştirmek için kullanılan bir tekniktir. Bu, genellikle HTTP POST isteği ile bir form gönderildiğinde veya bir API üzerinden gelen veri isteği işlendiğinde gerçekleşir.*

**Örneğin;**

Örnek bir model sınıfı:
```csharp
public class KullaniciModel
{
    public string Ad { get; set; }
    public string Soyad { get; set; }
    // ---
}
```
View'da model binding kullanımı:
```html
@model KullaniciModel //asp-for tag helper'ı ile eşleşir.(Model Binding)

<form asp-action="SetProduct" asp-controller="Product" method="post">
    <input type="text" asp-for="Ad"/> //asp-for tag helper'ı model binding yapmaya yarar.
    <br />
    <input type="text" asp-for="Soyad"/>
    <br />
    @*---*@
    <button type="submit">Submit</button>
</form>
```
Controller'da model binding kullanımı:
```csharp
[HttpPost]
// model ile formdan yollanan bilgiler yakalanır.
public IActionResult KullaniciKaydet(KullaniciModel model)
{
    // model üzerinde işlemler yapabilirsiniz
    // model.Ad, model.Soyad, model.Eposta gibi özelliklere erişebilirsiniz
    // ...
    return View();
}
```
