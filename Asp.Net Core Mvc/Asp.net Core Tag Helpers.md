# Tag Helpers
**Tag Helper nedir ve yararları nelerdir?**

*Tag helper server-side kodlarımızı html tagleri gibi oluşturup kullanmamızı sağlar.  Bu da bizlere  **View**'ın daha okunabilir, anlaşılabilir ve kolay geliştirilebilir hale getirmesini sağlamaktadır.*

**Örneğin;**

```csharp
  @Html.ActionLink("Go to index","Index","Home")
```
```html
  <a asp-action="index" asp-controller="home" >Go to index</a>
```

## asp-controller / asp-action / asp-route
- **asp-controller:** *Yönlendirilecek controller'ı ifade eder.*
- **asp-action :** *Yönlendirilecek action'ı ifade eder.*
- **asp-route:** *İlgili metoda parametre ile istek oluşturma.*
```html
<form asp-controller="Admin" asp-action="UpdateCategories" asp-route-id=@category.Id method="get">
```
## asp-for
- *`<input>` tag'inin içinde kullanılırsa Model binding yapmaya yarar.* 
```csharp
@model Models.CategoryCreateVM
```
```html
<input asp-for="Name" type="text">
```
- *`<label>` tag'inin içinde kullanılırsa ilgili prop'un validation'ının display değerini döndürür.*
```html
<label asp-for="OgrAd" class="control-label"></label>
```
## asp-validation-for
*Validation işlemleri sırasında fırlatılan hata mesajını yakalar ve yazdırır.*
```html
<span class="text-danger" asp-validation-for="Name"></span>
```
