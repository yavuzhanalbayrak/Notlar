# Relationships(İlişkiler)
  
SQL ilişkileri (relationships), bir veritabanındaki tablolar arasındaki bağlantıları ifade eder. İlişkiler, veritabanı tasarımının temel bir unsuru olarak kullanılır ve veri bütünlüğünü sağlamak, veri tekrarını önlemek ve veri modelini daha etkili bir şekilde organize etmek için önemlidir.
## Relationships(İlişkiler) Terimleri
#### Principal Entity(Asıl Entity)
Kendi başına var olabilen tabloyu modelleyen entity'e denir.
Departmanlar tablosunu modelleyen 'Departman' entity'sidir.

#### Dependent Entity(Bağımlı Entity)
Kendi başına var olamayan, bir başka tabloya bağımlı(ilişkisel olarak bğaımlı) olan tabloyu modelleyen entity'e denir.
Calisanlar tablosunu modelleyen 'Calisan' entity'sidir.

#### Foreign Key
Principal Entity ile Dependent Entity arasındaki ilişkiyi sağlayan key'dir.

Dependent Entity'de tanımlanır.
Principal Entity'de ki Principal Key'i tutar.

#### Principal Key
Principal Entity'deki id'nin kendisidir. Principal Entity'nin kimliği olan kolonu ifade eden propertydir.

```csharp
class Calisan
{
    public int Id { get; set; }
    public string CalisanAdi { get; set; }
    public int DepartmanId { get; set; }

    public Departman Departman { get; set; }
}
```

```csharp
class Departman
{
    public int Id { get; set; }
    public string DepartmanAdi { get; set; }

    public List<Calisan> Calisanlar { get; set; }
}

```




#### Navigation Property Nedir?
İlişkisel tablolar arasındaki fiziksel erişimi entity class'ları üzerinden sağlayan property'lerdir.

Bir property'nin navigation property olabilmesi için kesinlikle entity türünden olması gerekiyor.

Navigation property'ler entity'lerdeki tanımlarına göre n'e n yahut 1'e n şeklinde ilişki türlerini ifade etmektedirler.


## İlişki Türleri
#### One to One
Çalışan ile adresi arasındaki ilişki.


#### One to Many
Çalışan ile departman arasındaki ilişki.

#### Many to Many
Çalışanlar ile projeler arasındaki ilişki.

## Entity Framework Core'da İlişki Yapılandırma Yöntemleri
#### Default Conventions
Varsayılan entity kurallarını kullanarak yapılan ilişki yapılandırma yöntemleridir.

Navigation property'leri kullanarak ilişki şablonlarını çıkarmaktadır.

#### Data Annotations Attributes
Entity'nin niteliklerine göre ince ayarlar yapmamızı sağlayan attribute'lardır. [Key], [ForeignKey]


#### Fluent API

Entity modellerindeki ilişkileri yapılandırırken daha detaylı çalışmamızı sağlayan yöntemdir.

#### HasOne
İlgili entity'nin ilişkisel entity'ye birebir ya da bire çok olacak şekilde ilişkisini yapılandırmaya başlayan metottur.

#### HasMany
İlgili entity'nin ilişkisel entity'ye çoka bir ya da çoka çok olacak şekilde ilişkisini yapıulandırmaya başlayan metottur.

#### WithOne
HasOne ya da HasMany'den sonra bire bir ya da çoka bir olacak şekilde ilişki yapılandırmasını tamamlayan metottur.

#### WithMany
HasOne ya da HasMany'den sonra bire çok ya da çoka çok olacak şekilde ilişki yapılandırmasını tamamlayan metottur.

## One to One

#### Default Convention
- Her iki entity'de Navigation Property ile birbirlerini tekil olarak referans ederek fiziksel bir ilişkinin olacağı ifade edilir.
- One to One ilişki türünde, dependent entity'nin hangisi olduğunu default olarak belirleyebilmek pek kolay değildir. Bu durumda fiziksel olarak bir foreign key'e karşılık property/kolon tanımlayarak çözüm getirebiliyoruz.
- foreign key'e karşılık property tanımlayarak lüzumsuz bir kolon oluşturmuş oluyoruz.
```csharp
class Calisan
{
    public int Id { get; set; }
    public string Adi { get; set; }

    public CalisanAdresi CalisanAdresi { get; set; }
}
class CalisanAdresi
{
    public int Id { get; set; }
    public int CalisanId { get; set; }
    public string Adres { get; set; }

    public Calisan Calisan { get; set; }
}
```
#### Data Annotations
- Navigation Property'ler tanımlanmalıdır.
- Foreign kolonunun ismi default convention'ın dışında bir kolon olacaksa eğer ForeignKey attribute ile bunu bildirebiliriz.
- Foreign Key kolonu oluşturulmak zorunda değildir. 
- 1'e 1 ilişkide ekstradan foreign key kolonuna ihtiyaç olmayacağından dolayı dependent entity'deki id kolonunun hem foreign key hem de primary key olarak kullanmayı tercih ediyoruz ve bu duruma özen gösterilidir diyoruz.
```csharp
class Calisan
{
    public int Id { get; set; }
    public string Adi { get; set; }

    public CalisanAdresi CalisanAdresi { get; set; }
}
class CalisanAdresi
{
    [Key, ForeignKey(nameof(Calisan))]
    public int Id { get; set; }
    public string Adres { get; set; }

    public Calisan Calisan { get; set; }
}
```
#### Fluent API
- Navigation Propertyler tanımlanmalı!
- Fluent API yönteminde entity'ler arasındaki ilişki context sınıfı içerisinde OnModelCreating fonksiyonun override edilerek metotlar aracılığıyla tasarlanması gerekmektedir. Yani tüm sorumluluk bu fonksiyon içerisindeki çalışmalardadır.
```csharp
class Calisan
{
    public int Id { get; set; }
    public string Adi { get; set; }

    public CalisanAdresi CalisanAdresi { get; set; }
}
class CalisanAdresi
{
    public int Id { get; set; }
    public string Adres { get; set; }

    public Calisan Calisan { get; set; }
}
```
```csharp
class ESirketDbContext : DbContext
{
    public DbSet<Calisan> Calisanlar { get; set; }
    public DbSet<CalisanAdresi> CalisanAdresleri { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer("Server=localhost, 1433;Database=ESirketDB;User ID=SA;Password=1q2w3e4r+!");
    }
    //Model'ların(entity) veritabanında generate edilecek yapıları bu fonksiyonda içerisinde konfigüre edilir
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<CalisanAdresi>()
            .HasKey(c => c.Id);

        modelBuilder.Entity<Calisan>()
             .HasOne(c => c.CalisanAdresi)
             .WithOne(c => c.Calisan)
             .HasForeignKey<CalisanAdresi>(c => c.Id);
    }
}
```
## One to Many
#### Default Convention
Default convention yönteminde bire çok ilişkiyi kurarken foreign key kolonuna karşılık gelen bir property tanımlamak mecburiyetinde değilidiz. Eğer tanımlamazsak EF Core bunu kendisi tamamlayacak yok eğer tanımlarsak, tanımladığımızı baz alacaktır.
```csharp
class Calisan //Dependent Entity
{
    public int Id { get; set; }
    public int DepartmanId { get; set; }
    public string Adi { get; set; }

    public Departman Departman { get; set; }
}
class Departman
{
    public int Id { get; set; }
    public string DepartmanAdi { get; set; }

    public ICollection<Calisan> Calisanlar { get; set; }

}
```
#### Data Annotations
Default convention yönteminde foreign key kolonuna karşılık gelen property'i tanımladığımızda bu property ismi temel geleneksel entity tanımlama kurallarına uymuyorsa eğer Data Annotations'lar ile müdahalede bulunabiliriz."
```csharp
class Calisan //Dependent Entity
{
    public int Id { get; set; }
    [ForeignKey(nameof(Departman))]
    public int DId { get; set; }

    public string Adi { get; set; }

    public Departman Departman { get; set; }
}
class Departman
{
    public int Id { get; set; }
    public string DepartmanAdi { get; set; }

    public ICollection<Calisan> Calisanlar { get; set; }

}
```
#### Fluent API
```csharp
using System.Collections.Generic;
using System.Reflection.Emit;

class Calisan //Dependent Entity
{
    public int Id { get; set; }
    public int DId { get; set; }
    public string Adi { get; set; }

    public Departman Departman { get; set; }
}
class Departman
{
    public int Id { get; set; }
    public string DepartmanAdi { get; set; }

    public ICollection<Calisan> Calisanlar { get; set; }

}
```
```csharp
class ESirketDbContext : DbContext
{
    public DbSet<Calisan> Calisanlar { get; set; }
    public DbSet<Departman> Departmanlar { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer("Server=localhost, 1433;Database=ESirketDB;User ID=SA;Password=1q2w3e4r+!");
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Calisan>()
            .HasOne(c => c.Departman)
            .WithMany(d => d.Calisanlar)
            .HasForeignKey(c => c.DId);
    }
}
```
## Many to Many

#### Default Convention
- İki entity arasındaki ilişkiyi navigation propertyler üzerinden çoğul olarak kurmalıyız. (ICollection, List)
- Default Convention'da cross table'ı manuel oluşturmak zorunda değiliz. EF Core tasarıma uygun bir şekilde cross table'ı kendisi otomatik basacak ve generate edecektir.
- Ve oluşturulan cross table'ın içerisinde composite primary key'i de otomatik oluşturmuş olacaktır.
```csharp
class Kitap
{
    public int Id { get; set; }
    public string KitapAdi { get; set; }

    public List<Yazar> Yazarlar { get; set; }
}
class Yazar
{
    public int Id { get; set; }
    public string YazarAdi { get; set; }

    public List<Kitap> Kitaplar { get; set; }
}
```
#### Data Annotations
- Cross table manuel olarak oluşturulmak zorundadır.
- Entity'lerde oluşturduğumuz cross table entity si ile bire çok bir ilişki kurulmalı.
- CT'da composite primary key'i data annotation(Attributes)lar ile manuel kuramıyoruz. Bunun için de Fluent API'da çalışma yaopmamız gerekiyor.
- Cross table'a karşılık bir entity modeli oluşturuyorsak eğer bunu context sınıfı içerisinde DbSet property'si olarak bildirmek mecburiyetinde değiliz!
```csharp
class Kitap
{
    public int Id { get; set; }
    public string KitapAdi { get; set; }

    public ICollection<KitapYazar> Yazarlar { get; set; }
}
//Cross Table
class KitapYazar
{
    [ForeignKey(nameof(Kitap))]
    public int KId { get; set; }

    [ForeignKey(nameof(Yazar))]
    public int YId { get; set; }

    public Kitap Kitap { get; set; }
    public Yazar Yazar { get; set; }
}
class Yazar
{
    public int Id { get; set; }
    public string YazarAdi { get; set; }

    public ICollection<KitapYazar> Kitaplar { get; set; }
}
```
#### Fluent API
- Cross table manuel oluşturulmalı
- DbSet olarak eklenmesine lüzum yok,
- Composite PK Haskey metodu ile kurulmalı!
```csharp
class Kitap
{
    public int Id { get; set; }
    public string KitapAdi { get; set; }

    public ICollection<KitapYazar> Yazarlar { get; set; }
}
// Cross Table
class KitapYazar
{
    public int KitapId { get; set; }
    public int YazarId { get; set; }

    public Kitap Kitap { get; set; }
    public Yazar Yazar { get; set; }
}
class Yazar
{
    public int Id { get; set; }
    public string YazarAdi { get; set; }

    public ICollection<KitapYazar> Kitaplar { get; set; }
}
```
```csharp
class EKitapDbContext : DbContext
{
    public DbSet<Kitap> Kitaplar { get; set; }
    public DbSet<Yazar> Yazarlar { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer("Server=localhost, 1433;Database=EKitapDB;User ID=SA;Password=1q2w3e4r+!");
    }
    //Data Annotations
    //protected override void OnModelCreating(ModelBuilder modelBuilder)
    //{
    //    modelBuilder.Entity<KitapYazar>()
    //        .HasKey(ky => new { ky.KId, ky.YId });
    //}

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<KitapYazar>()
            .HasKey(ky => new { ky.KitapId, ky.YazarId });

        modelBuilder.Entity<KitapYazar>()
            .HasOne(ky => ky.Kitap)
            .WithMany(k => k.Yazarlar)
            .HasForeignKey(ky => ky.KitapId);

        modelBuilder.Entity<KitapYazar>()
            .HasOne(ky => ky.Yazar)
            .WithMany(y => y.Kitaplar)
            .HasForeignKey(ky => ky.YazarId);
    }
}
```
