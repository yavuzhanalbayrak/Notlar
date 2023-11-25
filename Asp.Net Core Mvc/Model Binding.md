# Model Binding
**Model Binding:** *HTTP request ile gelen verilerin ayrıştırılarak ilgili Controller'daki bulunan action metotlarda uygun herhangi bir türe dönüştürülmesi işlemidir*.

**Örneğin;**
```html
@model Product //asp-for tag helper'ı ile eşleşir.(Model Binding)

<form asp-action="SetProduct" asp-controller="Product" method="post">
    <input type="text" asp-for="ProductName" placeholder="Product Name" /> //asp-for tag helper'ı model binding yapmaya yarar.
    <br />
    <input type="number" asp-for="ProductQuantity" placeholder="Quantity" />
    <br />
    <button type="submit">Submit</button>
</form>
```
