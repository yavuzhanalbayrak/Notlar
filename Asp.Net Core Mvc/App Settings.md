# App Settings
`appsettings.json`, bir ASP.NET Core uygulamasının yapılandırma bilgilerini içeren bir JSON dosyasıdır. Bu dosya, uygulama tarafından kullanılan çeşitli ayarları, bağlantı dizgilerini, servis yapılandırmalarını ve diğer parametreleri içerebilir.

Bu dosya, uygulamanın çalışma zamanındaki bazı temel konfigürasyonları saklamak ve bu konfigürasyonları kolayca değiştirmek için kullanılır. Genellikle, bu dosyada uygulama ayarlarını yönetmek, geliştirmede ve dağıtımda daha esnek bir yaklaşım sunar.

Bir örnek `appsettings.json` dosyası şu şekilde olabilir:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;"
  },
  "AppSettings": {
    "ApiUrl": "https://api.example.com",
    "MaxItemCount": 50
  }
}
```

Bu örnekte:

- `"Logging"` altındaki yapılandırma, uygulamanın günlük (log) seviyelerini belirler.
- `"AllowedHosts"` altındaki yapılandırma, hangi ana bilgisayar adlarının uygulamaya izin verildiğini belirler.
- `"ConnectionStrings"` altındaki yapılandırma, veritabanı bağlantı dizesini içerir.
- `"AppSettings"` altındaki yapılandırma, özel uygulama ayarlarını içerir.

Bu dosyadaki değerlere uygulama kodundan erişebilmek için, `Program.cs` dosyasındaki `builder.Services` metodunda bir konfigürasyon servisi yapılandırılır:

```csharp
var configuration = builder.Configuration; 
builder.Services.Configure<User>(configuration.GetSection("Person"));
```

Bu örnekte, `"AppSettings"` altındaki yapılandırmaları `AppSettings` adlı bir sınıfa bağlamak için `services.Configure` kullanılmaktadır. `AppSettings` sınıfı, `appsettings.json` dosyasındaki `"AppSettings"` altındaki yapılandırmalara karşılık gelir ve uygulama kodunda bu ayarlara erişmek için kullanılabilir.

Uygulama kodunda `IConfiguration` servisini enjekte ederek veya `IOptions<T>` kullanarak `appsettings.json` dosyasındaki ayarlara erişebilirsiniz.

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _configuration;
    private readonly User _userConfig;
    //Program.cs'den IOptions<User> türünden veri gelir ve bu veri değişkende saklanır.
    public HomeController(IConfiguration configuration,IOptions<User> userConfig)
    {
        _configuration = configuration;
        _userConfig = userConfig.Value;
    }

    public IActionResult Index()
    {
        //appsettings.json dosyasından veri okuma
        var name = _configuration["Person:Name"];
        var surname = _configuration["Person:Surname"];
        var person = _configuration.GetSection("Person");
        //Veriyi bir nesneye atama
        var user = _configuration.GetSection("Person").Get<User>();
        
        //options ile appsettings.json'daki veriyi container'dan okuma(Önerilen)
        User user2 = _userConfig;

	 }
}
```
