# Connection Resiliency

### Connection Resiliency Nedir?
- EF Core üzerinde yapılan veritabanı çalışmaları sürecinde ister istemez veritabanı bağlantısında kopuşlar/kesintiler vs. meydana gelebilmektedir. 

- Connection Resiliency ile kopan bağlantıyı tekrar kurmak için gerekli tekrar bağlantı taleplerinde bulunabilir ve biryandan da execution strategy dediğimiz davranış modellerini belirleyerek bağlantıların kopması durumunda tekrar edecek olan sorguları baştan sona yeniden tetikleyebiliriz.

#### EnableRetryOnFailure
- Uygulama sürecinde veritabanı bağlantısı koptuğu taktirde bu yapılandırma sayesinde bağlantıyı tekrardan kurmaya çalışabiliyoruz.
```csharp
optionsBuilder.UseSqlServer("Server=localhost, 1433;Database=ApplicationDB;User ID=SA;Password=1q2w3e4r+!;TrustServerCertificate=True", builder => builder.EnableRetryOnFailure(
            maxRetryCount: 5,
            maxRetryDelay: TimeSpan.FromSeconds(15),
            errorNumbersToAdd: new[] { 4060 }))
            .LogTo(
            filter: (eventId, level) => eventId.Id == CoreEventId.ExecutionStrategyRetrying,
            logger: eventData =>
            {
                Console.WriteLine($"Bağlantı tekrar kurulmaktadır.");
            });
```

#### MaxRetryCount
- Yeniden bağlantı sağlanması durumunun kaç kere gerçekleştirileceğini bildirmektedir.
- **`EnableRetryOnFailure()`** fonksiyonunun parametresidir.
- Defualt değeri 6'dır.
#### MaxRetryDelay
- Yeniden bağlantı sağlanması periyodunu bildirmektedir.
- **`EnableRetryOnFailure()`** fonksiyonunun parametresidir.
- Default değeri 30'dur.

### Execution Strategies
- EF Core ile yapılan bir işlem sürecinde veritabanı bağlatısı koptuğu taktirde yeniden bağlantı denenirken yapılan davranışa/alınan aksiyona Execution Strategy denmektedir.

- Bu stratejiyi default değerlerde kullanabileceğimiz gibi custom olarak da kendimize göre özelleştirebilir ve bağlantı koptuğu durumlarda istediğimiz aksiyonları alabiliriz.

### Default Execution Strategy
- Eğer ki Connection Resiliency için **`EnableRetryOnFailure()`** metodunu kullanıyorsak bu default execution strategy karşılık gelecektir.
- MaxRetryCount : 6
- Delay: 30
Default değerlerin kullanılabilmesi için **`EnableRetryOnFailure()`** metodunun parametresis overload'ının kullanılması gerekmektedir.

### Custom Execution Strategy

#### Oluşturma:
```csharp
class CustomExecutionStrategy : ExecutionStrategy
{
    public CustomExecutionStrategy(ExecutionStrategyDependencies dependencies, int maxRetryCount, TimeSpan maxRetryDelay) : base(dependencies, maxRetryCount, maxRetryDelay)
    {
    }

    public CustomExecutionStrategy(DbContext context, int maxRetryCount, TimeSpan maxRetryDelay) : base(context, maxRetryCount, maxRetryDelay)
    {
    }

    int retryCount = 0;
    protected override bool ShouldRetryOn(Exception exception)
    {
        Yeniden bağlantı durumunun söz konusu olduğu anlarda yapılacak işlemler...
        Console.WriteLine($"#{++retryCount}. Bağlantı tekrar kuruluyor...");
        return true;
    }
}
```

#### Kullanma - ExecutionStrategy:
```csharp
optionsBuilder.UseSqlServer("Server=localhost, 1433;Database=ApplicationDB;User ID=SA;Password=1q2w3e4r+!;TrustServerCertificate=True", builder => builder.ExecutionStrategy(dependencies => new CustomExecutionStrategy(dependencies, 10, TimeSpan.FromSeconds(15))));
```

### Bağlantı Koptuğu Anda Execute Edilmesi Gereken Tüm Çalışmaları Tekrar İşlemek
- EF Core ile yapılan çalışma sürecinde veritabanı bağlantısının kesildiği durumlarda, bazen bağlantının tekrardan kurulması tek başına yetmemekte, kesintinin olduğu çalışmanın da baştan tekrardan işlenmesi gerekebilmektedir. İşte bu tarz durumlara karşılık EF Core **`Execute`** - **`ExecuteAsync`** fonksiyonunu bizlere sunmaktadır.

- **`Execute`** fonksiyonu, içerisine vermiş olduğumuz kodları **`commit`** edilene kadar işleyecektir. Eğer ki bağlantı kesilmesi meydana gelirse, bağlantının tekrardan kurulması durumunda **`Execute`** içerisindeki çalışmalar tekrar baştan işlenecek ve böylece yapılan işlemin tutarlılığı için gerekli çalışma sağlanmış olacaktır.
```csharp
var strategy = context.Database.CreateExecutionStrategy();
await strategy.ExecuteAsync(async () =>
{
    using var transcation = await context.Database.BeginTransactionAsync();
    await context.Persons.AddAsync(new() { Name = "Hilmi" });
    await context.SaveChangesAsync();

    await context.Persons.AddAsync(new Person() { Name = "Şuayip" });
    await context.SaveChangesAsync();

    await transcation.CommitAsync();
});
```

### Execution Strategy Hangi Durumlarda Kullanılır?
- Veritabanının şifresi belirli periyotlarda otomatik olarak değişen uygulamalarda güncel şifreyle connection string'i sağlayacak bir operasyonu custom execution strategy belirleyerek gerçekleştitrebilirsiniz.

