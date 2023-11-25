# View Start
ASP.NET Core MVC uygulamalarında `_ViewStart.cshtml` dosyası, bir görünüm (view) klasörü veya alt klasörü içindeki tüm Razor dosyalarına genel ayarları uygulamak için kullanılır. Bu dosya, belirli bir klasör veya alt klasördeki tüm görünümlere ortak bir başlangıç noktası sağlamak için kullanılır.

`_ViewStart.cshtml` dosyasında belirlenen ayarlar, bu klasör veya alt klasördeki Razor dosyalarının render edilirken uygulanacak olan varsayılan ayarları belirler. Bu genellikle layout (düzen) belirleme, tema seçimi, ortak view model belirleme veya benzeri ayarlar için kullanılır.

Örneğin, `_ViewStart.cshtml` dosyasında bir layout belirleyebilirsiniz:

```csharp
@{
    Layout = "_SharedLayout";
}
```

Bu durumda, `_SharedLayout.cshtml` dosyası, bu klasör veya alt klasördeki tüm Razor dosyalarında varsayılan layout olarak kullanılacaktır.

`_ViewStart.cshtml` dosyası, bir uygulamanın farklı bölümlerine özel view ayarlarını belirtmek, düzenleri sınıflandırmak veya tema seçimini yönetmek gibi senaryolarda kullanışlıdır. Ayrıca, bu dosya, görünümleri düzenleme ve uygulama genelinde tutarlılık sağlama amacıyla kullanılan bir araçtır.

Ancak, belirtmeye değer bir diğer önemli dosya `_ViewImports.cshtml`'dir. `_ViewStart.cshtml` dosyasında belirtilen ayarlar, `_ViewImports.cshtml` dosyasındaki import beyanları ve diğer ayarlarla birleştirilir, bu nedenle bu iki dosya birlikte kullanıldığında güçlü bir konfigürasyon sağlanabilir.
