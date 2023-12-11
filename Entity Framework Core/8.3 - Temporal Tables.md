# Temporal Tables

### Temporal Tables Nedir?
- Veri değişikliği süreçlerinde kayıtları depolayan ve zaman içinde farklı noktalardaki tablo verilerinin analizi için kullanılan ve sistem tarafından yönetilen tablolardır.
- EF Core 6.0 ile desteklenmektedir.

### Temporal Tables Özelliğiyle Nasıl Çalışılır?
- EF Core'daki migration yapıları sayesinde tempral table'lar oluşturulup veritabanında üretilebilmektedir.
- Mevcut tabloları migration'lar aracılığıyla Temporal Table'lara dönüştürülebilmektedir.
- Herhangi bir tablonun verisel olarak geçmişini rahatlıkla sorgulayabiliriz.
- Herhangi bir tablodaki bir verinin geçmişteki herhangi bir T anındaki hali/durumu/verileri geri getirilebilmektedir.

### Temporal Table Nasıl Uygulanır?

#### IsTemoral Yapılandırması
- EF Core bu yapılandırma fonksiyonu sayesinde ilgili entity'e karşılık üretilecek tabloda temporal table oluşturacağını anlamaktadır. Yahut önceden ilgili tablo üretilmişse eğer onu temporal table'a dönüştürecektir.
```csharp
class TemporalExampleDb : DbContext
{
    public DbSet<Post> Posts { get; set; }
    
    // ...
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .ToTable("Posts", t => t.IsTemporal());
    }
}
```
#### Temporal Table İçin Üretilen Migration'ın İncelenmesi
- Yukarıdaki kod bloğunu incelerseniz eğer override edilmiş olan ‘OnModelCreating’ içerisinde ilgili model üzerinde ‘ToTable’ fonksiyonu eşliğinde ‘IsTemporal’ fonksiyonunu tetikleyerek bu tablonun bir temporal table’ının olacağı bildirilmektedir. Bu değişiklikten sonra yeni bir migration basıldığı taktirde içeriği aşağıdaki gibi olacaktır.

- ![Entity Framework Core - Temporal Tables İle Çalışmak](https://www.gencayyildiz.com/blog/wp-content/uploads/2022/01/Entity-Framework-Core-Temporal-Tables-Ile-Calismak.png)
- Yukarıdaki görseli yorumlarsak eğer; sarı ile vurgulanmış kısımda ‘Posts’ tablosu bir temporal table olarak ayarlanmakta ve gerekli açıklamalar yapılmaktadır. Yeşil ve mor kısımlarda ise yapılan verisel değişikliklerin hangi zamanlarda olduğuna dair kayıtları tutacak ‘PeriodStart’ ve ‘PeriodEnd’ kolonları eklenmektedir.
- Bu vaziyette oluşturulmuş migration’ı migrate edersek eğer ilgili tablo aşağıdaki gibi sunucuya yansıyacaktır.  

- ![Entity Framework Core - Temporal Tables İle Çalışmak](https://www.gencayyildiz.com/blog/wp-content/uploads/2022/01/Entity-Framework-Core-Temporal-Tables-Ile-Calismak-2.png)

### Temporal Table'ı Test Edelim

#### Veri Eklerken
- Temporal Table'a veri ekleme süreçlerinde herhangi bir kayıt atılmaz! 
- Temporal Table'ın yapısı, var olan veriler üzerindeki zamansal değişimleri takip etmek üzerine kuruludur!
```csharp
Post p1 = new() { Content = "A Post Content", PostType = PostType.Talk, Title = "A Post Title" };
Post p2 = new() { Content = "B Post Content", PostType = PostType.Talk, Title = "B Post Title" };
Post p3 = new() { Content = "C Post Content", PostType = PostType.Talk, Title = "C Post Title" };
 
TemporalExampleDb context = new();
await context.AddRangeAsync(p1, p2, p3);
await context.SaveChangesAsync();
};

await context.Persons.AddRangeAsync(persons);
await context.SaveChangesAsync();
```
#### Veri Güncellerken
```csharp
Post post = await context.Posts.FindAsync(2);
post.PostType = PostType.Side;
post.Title = "B Post Title - Updated";
 
await context.SaveChangesAsync();
```
![Entity Framework Core - Temporal Tables İle Çalışmak](https://www.gencayyildiz.com/blog/wp-content/uploads/2022/01/Entity-Framework-Core-Temporal-Tables-Ile-Calismak-3.png)
- Görüldüğü üzere ‘2’ id değerine sahip verinin önceki kaydı ve hangi zaman aralığında bu değişikliğin yapıldığı temporal table’a kaydedilmiştir. Şimdi ilgili veri üzerinde bir güncelleme işlemi daha gerçekleştirip, inceleyelim.
#### Veri Silerken
```csharp
Post post = await context.Posts.FindAsync(2);
context.Posts.Remove(post);
 
await context.SaveChangesAsync();
```
![Entity Framework Core - Temporal Tables İle Çalışmak](https://www.gencayyildiz.com/blog/wp-content/uploads/2022/01/Entity-Framework-Core-Temporal-Tables-Ile-Calismak-5.png)
- İlgili veri silinirken son hali temporal table’a kopyalanır ve ardından kaldırılır.
### Temporal Table Üzerinden Geçmiş Verisel İzleri Sorgulama

#### TemporalAsOf
- Belirli bir zaman için değişikiğe uğrayan tüm öğeleri döndüren bir fonksiyondur.
```csharp
var posts = await context.Posts.TemporalAsOf(new DateTime(2022, 01, 23, 08, 49, 45)).Select(data => new
{
    Post = data,
    PeriodStart = EF.Property<DateTime>(data, "PeriodStart"),
    PeriodEnd = EF.Property<DateTime>(data, "PeriodEnd"),
}).ToListAsync();
 
foreach (var post in posts)
{
    Console.WriteLine($"Id\t\t: {post.Post.Id}");
    Console.WriteLine($"Title\t\t: {post.Post.Title}");
    Console.WriteLine($"Content\t\t: {post.Post.Content}");
    Console.WriteLine($"PeriodStart\t: {post.PeriodStart}");
    Console.WriteLine($"PeriodEnd\t: {post.PeriodEnd}");
    Console.WriteLine("**********");
}
```
![Entity Framework Core - Temporal Tables İle Çalışmak](https://www.gencayyildiz.com/blog/wp-content/uploads/2022/01/Entity-Framework-Core-Temporal-Tables-Ile-Calismak-7.png)
#### TemporalAll
- Güncellenmiş yahut silinmiş olan tüm verilerin geçmiş sürümlerini veya geçerli durumlarını döndüren bir fonksiyondur.
```csharp
var posts = await context.Posts.TemporalAll().Select(data => new
{
    Post = data,
    PeriodStart = EF.Property<DateTime>(data, "PeriodStart"),
    PeriodEnd = EF.Property<DateTime>(data, "PeriodEnd"),
}).ToListAsync();
 
foreach (var post in posts)
{
    Console.WriteLine($"Id\t\t: {post.Post.Id}");
    Console.WriteLine($"Title\t\t: {post.Post.Title}");
    Console.WriteLine($"Content\t\t: {post.Post.Content}");
    Console.WriteLine($"PeriodStart\t: {post.PeriodStart}");
    Console.WriteLine($"PeriodEnd\t: {post.PeriodEnd}");
    Console.WriteLine("**********");
}
```
![Entity Framework Core - Temporal Tables İle Çalışmak](https://www.gencayyildiz.com/blog/wp-content/uploads/2022/01/Entity-Framework-Core-Temporal-Tables-Ile-Calismak-6.png)
#### TemporalFromTo
- Belirli bir zaman aralığı içerisindelki verileri döndüren fonksiyondur. 
- Başlangıç ve bitiş zamanı dahil değildir.
```csharp
// Başlangıç : 2022 - 12 - 09 05:29:55.0953716
var baslangic = new DateTime(2022, 12, 09, 05, 29, 55);
// Bitiş: 2022 - 12 - 09 05:30:30.3459797
var bitis = new DateTime(2022, 12, 09, 05, 30, 30);
```
```csharp
var posts = await context.Posts.TemporalFromTo(baslangic, bitis).Select(data => new
{
    Post = data,
    PeriodStart = EF.Property<DateTime>(data, "PeriodStart"),
    PeriodEnd = EF.Property<DateTime>(data, "PeriodEnd"),
}).ToListAsync();
```
#### TemporalBetween
- Belirli bir zaman aralığı içerisindelki verileri döndüren fonksiyondur. 
- Başlangıç verisi dahil değil ve bitiş zamanı ise dahildir.
```csharp
// Başlangıç : 2022 - 12 - 09 05:29:55.0953716
var baslangic = new DateTime(2022, 12, 09, 05, 29, 55);
// Bitiş: 2022 - 12 - 09 05:30:30.3459797
var bitis = new DateTime(2022, 12, 09, 05, 30, 30);
```
```csharp
var posts = await context.Posts.TemporalBetween(baslangic, bitis).Select(data => new
{
    Post = data,
    PeriodStart = EF.Property<DateTime>(data, "PeriodStart"),
    PeriodEnd = EF.Property<DateTime>(data, "PeriodEnd"),
}).ToListAsync();
```
#### TemporalContainedIn
- Belirli bir zaman aralığı içerisindelki verileri döndüren fonksiyondur. 
- Başlangıç ve bitiş zamanı ise dahildir.
```csharp
// Başlangıç : 2022 - 12 - 09 05:29:55.0953716
var baslangic = new DateTime(2022, 12, 09, 05, 29, 55);
// Bitiş: 2022 - 12 - 09 05:30:30.3459797
var bitis = new DateTime(2022, 12, 09, 05, 30, 30);
```
```csharp
var posts = await context.Posts.TemporalContainedIn(baslangic, bitis).Select(data => new
{
    Post = data,
    PeriodStart = EF.Property<DateTime>(data, "PeriodStart"),
    PeriodEnd = EF.Property<DateTime>(data, "PeriodEnd"),
}).ToListAsync();
```
### Silinmiş Bir Veriyi Temporal Table'dan Geri Getirme
- Silinmiş bir veriyi temporal table'dan getirebilmek için öncelikle yapılması gereken ilgili verinin silindiği tarihi bulmamız gerekmektedir. 
- Ardından TemporalAsOf fonksiyonu ile silinen verinin geçmiş değeri elde edilebilir ve fiziksel tabloya bu veri taşınabilir.
```csharp
DateTime dateOfDelete = await context.Posts.TemporalAll()
    .Where(p => p.Id == 2)
    .OrderBy(p => EF.Property<DateTime>(p, "PeriodEnd"))
    // En sonuncu işlem yapılan tarihi alıyoruz.
    // Çünkü bu veri silindiyse muhtemelen en sonuncu
    // işlem yapılan tarihte silinmiştir.
    .Select(data => EF.Property<DateTime>(data, "PeriodEnd"))
    .LastAsync();
 
//Silinmeden önceki en son kayıt elde ediliyor.
Post deletedPost = await context.Posts.TemporalAsOf(dateOfDelete.AddMilliseconds(-1))
    .SingleAsync(data => data.Id == 2);
 
//Fiziksel tabloya ekleniyor.
await context.AddAsync(deletedPost);
 
//Aşağıdaki ExecuteSqlInterpolatedAsync fonksiyonu ile SET IDENTITY_INSERT komutunu tetikleyebilmek için connection'ın açılması gerekmektedir.
await context.Database.OpenConnectionAsync();
 
//SET IDENTITY_INSERT komutu çalıştırılıyor
await context.Database.ExecuteSqlInterpolatedAsync($"SET IDENTITY_INSERT dbo.Posts ON");
//Sorgu execute ediliyor.
await context.SaveChangesAsync();
//SET IDENTITY_INSERT komutu kapatılıyor.
await context.Database.ExecuteSqlInterpolatedAsync($"SET IDENTITY_INSERT dbo.Posts OFF");
```
#### SET IDENTITY_INSERT Konfigürasyonu
- Id ile veri ekleme sürecinde ilgili verinin id sütununa kayıt işleyebilmek için veriyi fiziksel tabloya taşıma işleminden önce veritabanı seviyesinde SET IDENTITY_INSERT komutu eşliğinde id bazlı veri ekleme işlemi aktifleştirilmelidir.

