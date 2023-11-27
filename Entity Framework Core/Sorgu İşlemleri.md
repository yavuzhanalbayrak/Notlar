
# Sorgu İşlemleri
```csharp
﻿using Microsoft.EntityFrameworkCore;
ETicaretContext context = new();
```
 **En Temel Basit Bir Sorgulama Nasıl Yapılır?**
```csharp
#region Method Syntax
var urunler = await context.Urunler.ToListAsync();
#endregion
#region Query Syntax
var urunler2 = await (from urun in context.Urunler
                      select urun).ToListAsync();
#endregion
```

**Sorguyu Execute Etmek İçin Ne Yapmamız Gerekmektedir?**

- **Deferred Execution(Ertelenmiş Çalışma):**
IQueryable çalışmalarında ilgili kod yazıldığı noktada tetiklenmez/çalıştırılmaz yani ilgili kod yazıldığı noktada sorguyu generate etmez! Nerede eder? Çalıştırıldığı/execute edildiği noktada tetiklenir! İşte bu durumada ertelenmiş çalışma denir!
- **Komutla Execute etme(TolistAsync())**:
```await urunler.ToListAsync();```
```csharp
int urunId = 5;
string urunAdi = "2";

//IQueryable
var urunler = from urun in context.Urunler
              where urun.Id > urunId && urun.UrunAdi.Contains(urunAdi)
              select urun;

urunId = 200;
urunAdi = "4";

//Deferred Execution(Ertelenmiş Çalışma)
//Parametrelerin son haline(urunId=200, urunAdi=4) göre sorgu execute edilir.
//Entity Framework foreach'i görünce kendi Execute(IEnumerable) edip sorguyu yapar.
foreach (Urun urun in urunler)
{
    Console.WriteLine(urun.UrunAdi);
}

//Komutla Execute etme(TolistAsync()).
await urunler.ToListAsync();
```
**IQueryable ve IEnumerable Nedir?**
```csharp
var urunler = await (from urun in context.Urunler
                     select urun).ToListAsync();

#region IQueryable
Sorguya karşılık gelir.
Ef core üzerinden yapılmış olan sorgunun execute edilmemiş halini ifade eder.
#endregion
#region IEnumerable
Sorgunun çalıştırılıp/execute edilip verilerin in memorye yüklenmiş halini ifade eder.
#endregion
```

**ToListAsync:**
Üretilen sorguyu execute ettirmemizi sağlayan fonksiyondur.
```csharp
#region Method Syntax
var urunler = context.Urunler.ToListAsync();
#endregion
#region Query Syntax
var urunler = (from urun in context.Urunler
               select urun).ToListAsync();
var urunler = from urun in context.Urunler
              select urun;
var datas = await urunler.ToListAsync();
#endregion
```
## Çoğul Veri Getiren Sorgulama Fonksiyonları
**Where:**
Oluşturulan sorguya where şartı eklememizi sağlayan bir fonksiyondur.

```csharp
#region Method Syntax
var urunler = await context.Urunler.Where(u => u.Id > 500).ToListAsync();
var urunler = await context.Urunler.Where(u => u.UrunAdi.StartsWith("a")).ToListAsync();
Console.WriteLine();
#endregion
#region Query Syntax
var urunler = from urun in context.Urunler
              where urun.Id > 500 && urun.UrunAdi.EndsWith("7")
              select urun;
var data = await urunler.ToListAsync();
Console.WriteLine();
#endregion
```

**OrderBy:**
Sorgu üzerinde sıralama yapmamızı sağlayan bir fonksiyondur. (Ascending)

```csharp
#region Method Syntax
var urunler = context.Urunler.Where(u => u.Id > 500 || u.UrunAdi.EndsWith("2")).OrderBy(u => u.UrunAdi);
#endregion
#region Query Syntax
var urunler2 = from urun in context.Urunler
               where urun.Id > 500 || urun.UrunAdi.StartsWith("2")
               orderby urun.UrunAdi
               select urun;
#endregion

await urunler.ToListAsync();
await urunler2.ToListAsync();
```

**ThenBy:**
OrderBy üzerinde yapılan sıralama işlemini farklı kolonlarada uygulamamızı sağlayan bir fonksiyondur. (Ascending) 

```csharp
var urunler = context.Urunler.Where(u => u.Id > 500 || u.UrunAdi.EndsWith("2")).OrderBy(u => u.UrunAdi).ThenBy(u => u.Fiyat).ThenBy(u => u.Id);

await urunler.ToListAsync();
```

**OrderByDescending:**
Descending olarak sıralama yapmamızı sağlayan bir fonksiyondur.

```csharp
#region Method Syntax
var urunler = await context.Urunler.OrderByDescending(u => u.Fiyat).ToListAsync();
#endregion
#region Query Syntax
var urunler = await (from urun in context.Urunler
                     orderby urun.UrunAdi descending
                     select urun).ToListAsync();
#endregion
```

**ThenByDescending:**
OrderByDescending üzerinde yapılan sıralama işlemini farklı kolonlarada uygulamamızı sağlayan bir fonksiyondur. (Ascending)
```csharp
var urunler = await context.Urunler.OrderByDescending(u => u.Id).ThenByDescending(u => u.Fiyat).ThenBy(u => u.UrunAdi).ToListAsync();
#endregion
```
# Tekil Veri Getiren Sorgulama Fonksiyonları
**Single:**
Yapılan sorguda sade ve sadece tek bir verinin gelmesi amaçlanıyorsa Single ya da SingleOrDefault fonksiyonları kullanılabilir.
```csharp
#region SingleAsync
//Eğer ki, sorgu neticesinde birden fazla veri geliyorsa ya da hiç gelmiyorsa her iki durumda da exception fırlatır.
var urun = await context.Urunler.SingleAsync(u => u.Id == 55);
#endregion

#region SingleOrDefaultAsync
//Eğer ki, sorgu neticesinde birden fazla veri geliyorsa exception fırlatır, hiç veri gelmiyorsa null döner.
var urun = await context.Urunler.SingleOrDefaultAsync(u => u.Id == 55);
#endregion
```
**First:**
Yapılan sorguda tek bir verinin gelmesi amaçlanıyorsa First ya da FirstOrDefault fonksiyonları kullanılabilir.
```csharp
#region FirstAsync
//Sorgu neticesinde elde edilen verilerden ilkini getirir. Eğer ki hiç veri gelmiyorsa hata fırlatır.
var urun = await context.Urunler.FirstAsync(u => u.Id == 55);
#endregion

#region FirstOrDefaultAsync
//Sorgu neticesinde elde edilen verilerden ilkini getirir. Eğer ki hiç veri gelmiyorsa null değerini döndürür.
var urun = await context.Urunler.FirstOrDefaultAsync(u => u.Id == 55);
#endregion
```
![](https://cdn.discordapp.com/attachments/1166081920724115598/1166141617942564924/image.png?ex=656e5303&is=655bde03&hm=fb8c608a590c0069e7ec843c57a213145d57cfeee88dab4010c893f1102243d9&)

**Find:**
Find fonksiyonu, primary key kolonuna özel hızlı bir şekilde sorgulama yapmamızı sağlayan bir fonksiyondur.
```csharp
#region FindAsync
Urun urun = await context.Urunler.FirstOrDefaultAsync(u => u.Id == 55);
Urun urun = await context.Urunler.FindAsync(55);

#region Composite Primary key Durumu
UrunParca u = await context.UrunParca.FindAsync(2, 5);
#endregion
#endregion
```
![](https://cdn.discordapp.com/attachments/1166081920724115598/1166143282959634522/image.png?ex=656e5490&is=655bdf90&hm=17937a9ed67ba7b42184eeace9fb7de7ac52bdcbb366f4abe150ff0737a1660c&)

**LastAsync:**
Sorgu neticesinde gelen verilerden en sonuncusunu getirir. Eğer ki hiç veri gelmiyorsa hata fırlatır. OrderBy kullanılması mecburidir.
```csharp
var urun = await context.Urunler.OrderBy(u => u.Fiyat).LastAsync(u => u.Id > 55);
#endregion

#region LastOrDefaultAsync
Sorgu neticesinde gelen verilerden en sonuncusunu getirir. Eğer ki hiç veri gelmiyorsa null döner. OrderBy kullanılması mecburidir.
var urun = await context.Urunler.OrderBy(u => u.Fiyat).LastOrDefaultAsync(u => u.Id > 55);
#endregion
```


# Diğer Sorgulama Fonksiyonları
**CountAsync:**
Oluşturulan sorgunun execute edilmesi neticesinde kaç adet satırın elde edileceğini sayısal olarak(int) bizlere bildiren fonksiyondur.
```csharp
var urunler = (await context.Urunler.ToListAsync()).Count();
var urunler = await context.Urunler.CountAsync();
```

**LongCountAsync:**
Oluşturulan sorgunun execute edilmesi neticesinde kaç adet satırın elde edileceğini sayısal olarak(long) bizlere bildiren fonksiyondur.
```csharp
var urunler = await context.Urunler.LongCountAsync(u => u.Fiyat > 5000);
```
**AnyAsync:**
Sorgu neticesinde verinin gelip gelmediğini bool türünde dönen fonksiyondur. 
```csharp
var urunler = await context.Urunler.Where(u => u.UrunAdi.Contains("1")).AnyAsync();
var urunler = await context.Urunler.AnyAsync(u => u.UrunAdi.Contains("1"));
```

**MaxAsync:**
Verilen kolondaki max değeri getirir.
```csharp
var fiyat = await context.Urunler.MaxAsync(u => u.Fiyat);
```

**MinAsync:**
Verilen kolondaki min değeri getirir.
```csharp
var fiyat = await context.Urunler.MinAsync(u => u.Fiyat);
```

**Distinct:**
Sorguda mükerrer kayıtlar varsa bunları tekilleştiren bir işleve sahip fonksiyondur.
```csharp
var urunler = await context.Urunler.Distinct().ToListAsync();
```

**AllAsync:**
Bir sorgu neticesinde gelen verilerin, verilen şarta uyup uymadığını kontrol etmektedir. Eğer ki tüm veriler şarta uyuyorsa true, uymuyorsa false döndürecektir.
```csharp
var m = await context.Urunler.AllAsync(u => u.Fiyat < 15000);
var m = await context.Urunler.AllAsync(u => u.UrunAdi.Contains("a"));
```

**SumAsync:**
Vermiş olduğumuz sayısal proeprtynin toplamını alır.
```csharp
var fiyatToplam = await context.Urunler.SumAsync(u => u.Fiyat);
```

**AverageAsync:**
Vermiş olduğumuz sayısal proeprtynin aritmatik ortalamasını alır.
```csharp
var aritmatikOrtalama = await context.Urunler.AverageAsync(u => u.Fiyat);
```

**Contains:**
Like '%...%' sorgusu oluşturmamızı sağlar.
```csharp
var urunler = await context.Urunler.Where(u => u.UrunAdi.Contains("7")).ToListAsync();
```

**StartsWith:**
Like '...%' sorgusu oluşturmamızı sağlar.
```csharp
var urunler = await context.Urunler.Where(u => u.UrunAdi.StartsWith("7")).ToListAsync();
```

**EndsWith:**
Like '%...' sorgusu oluşturmamızı sağlar.
```csharp
var urunler = await context.Urunler.Where(u => u.UrunAdi.EndsWith("7")).ToListAsync();
```

# Sorgu Sonucu Dönüşüm Fonksiyonları
Bu fonksiyonlar ile sorgu neticesinde elde edilen verileri isteğimiz doğrultuusnda farklı türlerde projectsiyon edebiliyoruz.


**ToDictionaryAsync:**
Sorgu neticesinde gelecek olan veriyi bir dictioanry olarak elde etmek/tutmak/karşılamak istiyorsak eğer kullanılır!
```csharp
var urunler = await context.Urunler.ToDictionaryAsync(u => u.UrunAdi, u => u.Fiyat);

//ToList ile aynı amaca hizmet etmektedir. Yani, oluşturulan sorguyu execute edip neticesini alırlar.
//ToList : Gelen sorgu neticesini entity türünde bir koleksiyona(List<TEntity>) dönüştürmekteyken,
//ToDictionary ise : Gelen sorgu neticesini Dictionary türünden bir koleksiyona dönüştürecektir.
```
**ToArrayAsync:**
Oluşturulan sorguyu dizi olarak elde eder.
ToList ile muadil amaca hizmet eder. Yani sorguyu execute eder lakin gelen sonucu entity dizisi  olarak elde eder.
```csharp
var urunler = await context.Urunler.ToArrayAsync();
```
**Select:**
Select fonksiyonunun işlevsel olarak birden fazla davranışı söz konusudur,
```csharp
//1. Select fonksiyonu, generate edilecek sorgunun çekilecek kolonlarını ayarlamamızı sağlamaktadır. 

var urunler = await context.Urunler.Select(u => new Urun
                                                {
                                                    Id = u.Id,
                                                    Fiyat = u.Fiyat
                                                }).ToListAsync();

//2.Select fonksiyonu, gelen verileri farklı türlerde karşılamamızı sağlar. T, anonim

var urunler = await context.Urunler.Select(u => new
                                                {
                                                    Id = u.Id,
                                                    Fiyat = u.Fiyat
                                                }).ToListAsync();


var urunler = await context.Urunler.Select(u => new UrunDetay
{
    Id = u.Id,
    Fiyat = u.Fiyat
}).ToListAsync();

```

**SelectMany:**
Select ile aynı amaca hizmet eder. Lakin, ilişkisel tablolar neticesinde gelen koleksiyonel verileri de tekilleştirip projeksiyon etmemizi sağlar.

```csharp
var urunler = await context.Urunler.Include(u => u.Parcalar).SelectMany(u => u.Parcalar, (u, p) => new
                                                           {
                                                               u.Id,
                                                               u.Fiyat,
                                                               p.ParcaAdi
                                                           }).ToListAsync();
```

**GroupBy:**
Gruplama yapmamızı sağlayan fonksiyondur.
```csharp
#region Method Syntax
var datas = await context.Urunler.GroupBy(u => u.Fiyat).Select(group => new
                                                      {
                                                          Count = group.Count(),
                                                          Fiyat = group.Key
                                                      }).ToListAsync();
#endregion
#region Query Syntax
var datas = await (from urun in context.Urunler
                   group urun by urun.Fiyat
            into @group
                   select new
                   {
                       Fiyat = @group.Key,
                       Count = @group.Count()
                   }).ToListAsync();
#endregion
```

**Foreach Fonksiyonu:**
Bir sorgulama fonksiyonu felan değildir!
Sorgulama neticesinde elde edilen koleksiyonel veriler üzerinde iterasyonel olarak dönmemizi ve teker teker verileri elde edip işlemler yapabilmemizi sağlayan bir fonksiyondur. foreach döngüsünün metot halidir!
```csharp
foreach (var item in datas)
{

}
datas.ForEach(x =>
{

});
```
**İlgili Entity ve Context sınıfı:**
```csharp

public class ETicaretContext : DbContext
{
    public DbSet<Urun> Urunler { get; set; }
    public DbSet<Parca> Parcalar { get; set; }
    public DbSet<UrunParca> UrunParca { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer("Server=localhost, 1433;Database=ETicaretDB;User ID=SA;Password=1q2w3e4r+!");
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<UrunParca>().HasKey(up => new { up.UrunId, up.ParcaId });
    }
}
public class Urun
{
    public int Id { get; set; }
    public string UrunAdi { get; set; }
    public float Fiyat { get; set; }

    public ICollection<Parca> Parcalar { get; set; }
}
public class Parca
{
    public int Id { get; set; }
    public string ParcaAdi { get; set; }
}
public class UrunParca
{
    public int UrunId { get; set; }
    public int ParcaId { get; set; }

    public Urun Urun { get; set; }
    public Parca Parca { get; set; }
}

public class UrunDetay
{
    public int Id { get; set; }
    public float Fiyat { get; set; }
}
```
