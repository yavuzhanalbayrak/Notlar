# View İmports

ASP.NET Core uygulamalarında, `_ViewImports.cshtml` dosyası, belirli bir görünüm klasöründeki diğer Razor dosyalarında kullanılacak ortak namespace'leri, tag helper'ları ve diğer önemli import'ları tanımlamak için kullanılır. Bu dosya, diğer Razor dosyalarında tekrar eden import beyanlarını merkezi bir konumda yönetmeyi sağlar.

Genellikle, `_ViewImports.cshtml` dosyasında tanımlanan import'lar, bir uygulamanın belirli bir görünüm klasörü veya alt dizini için paylaşılan ve kullanılabilir hale getirilen belirli ayarları temsil eder.

Örneğin, `_ViewImports.cshtml` dosyasını kullanarak belirli bir namespace veya tag helper'ı şu şekilde belirtebilirsiniz:

```csharp
@using MyApplication.Utilities
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

Bu durumda, `MyApplication.Utilities` namespace'i, `_ViewImports.cshtml` dosyasındaki bu belirtilen klasör veya alt dizindeki tüm Razor dosyalarında otomatik olarak kullanılabilir hale gelir. Aynı şekilde, `Microsoft.AspNetCore.Mvc.TagHelpers` namespace'i, `*` yıldız karakteriyle birlikte, bu klasör veya alt dizindeki tüm Razor dosyalarında otomatik olarak kullanılabilir hale gelir.

Bu dosya, ortak kullanılan import beyanlarını düzenli bir şekilde yönetmek ve tekrarı önlemek için oldukça kullanışlıdır. Ayrıca, bir uygulamanın farklı bölümleri için özel import ayarlarını belirtmek amacıyla kullanılabilir.
