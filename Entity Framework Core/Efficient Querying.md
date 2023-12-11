# Efficient Querying



### IQueryable - IEnumerable Farkı

- IQueryable, bu arayüz üzerinde yapılan işlemler direkt generate edilecek olan sorguya yansıtılacaktır.

- IEnumerable, bu arayüz üzerinde yapılan işlemler temel sorgu neticesinde gelen ve in-memorye yüklenen instance'lar üzerinde gerçekleştirilir. Yani sorguya yansıtılmaz.

- IQueryable ile yapılan sorgulama çalışmalarında sql sorguyu hedef verileri elde edecek şekilde generate edilecekken, IEnumerable ile yapılan sorgulama çalışmalarında sql daha geniş verileri getirebilecek şekilde execute edilerek hedef veriler in-memory'de ayıklanır.


#### IQueryable
```csharp
var persons = await context.Persons.Where(p => p.Name.Contains("a"))
                             .Take(3)
                             .ToListAsync();
```
#### IEnumerable
```csharp
var persons = context.Persons.Where(p => p.Name.Contains("a"))
                             .AsEnumerable()
                             .Take(3)
                             .ToList();
```
### Yalnızca İhtiyaç Olan Kolonları Listeleyin - Select
```csharp
var persons = await context.Persons.Select(p => new
{
    p.Name
}).ToListAsync();
```

### Result'ı Limitleyin - Take
```csharp
await context.Persons.Take(50).ToListAsync();
```

### Join Sorgularında Eager Loading Sürecinde Verileri Filtreleyin
```csharp
var persons = await context.Persons.Include(p => p.Orders
					               .Where(o => o.OrderId % 2 == 0)
                                   .OrderByDescending(o => o.OrderId)
                                   .Take(4))
					     		   .ToListAsync();

foreach (var person in persons)
{
    var orders = person.Orders.Where(o => o.CreatedDate.Year == 2022);
}
```

### Şartlara Bağlı Join Yapılacaksa Eğer Explicit Loading Kullanın
```csharp
var person = await context.Persons.FirstOrDefaultAsync(p => p.PersonId == 1);
if (person.Name == "Ayşe")
{
    //Order'larını getir...
    await context.Entry(person).Collection(p => p.Orders).LoadAsync();
}
```

### Lazy Loading Kullanırken Dikkatli Olun!
#### Riskli Durum
- Her döngü için veritabanında person verilerine sorgu gidecektir.
```csharp
var persons = await context.Persons.ToListAsync();

foreach (var person in persons)
{
    foreach (var order in person.Orders)
    {
        Console.WriteLine($"{person.Name} - {order.OrderId}");
    }
    Console.WriteLine("***********");
}
```
#### İdeal Durum
```csharp
var persons = await context.Persons.Select(p => new { p.Name, p.Orders }).ToListAsync();

foreach (var person in persons)
{
    foreach (var order in person.Orders)
    {
        Console.WriteLine($"{person.Name} - {order.OrderId}");
    }
    Console.WriteLine("***********");
}
```

### Asenkron Fonksiyonları Tercih Edin
- Daha performanslı ve az maliyetli çalışırlar.
```csharp
var person = await context.Persons.Take(50).ToListAsync();
```


