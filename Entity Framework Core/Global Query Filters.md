# Global Query Filters

### Global Query Filters Nedir?
- Bir entity'e özel uygulama seviyesinde genel/ön kabullü şartlar oluşturmamızı ve böylece verileri global bir şekilde filtrelememeizi sağlayan bir özelliktir.

- Böylece belirtilen entity üzerinden yapılan tm sorgulamalarda ekstradan bir şart ifadesine gerek kalmaksızın filtreleri otomatik uygulayarak hızlıca sorgulama yapmamızı sağlamaktadır.

- Genellikle uygulama bazında aktif(IsActive) gibi verilerle çalışıldığı durumlarda,
MultiTenancy uygulamalarda TenantId tanımlarken vs. kullanılabilri.

### Global Query Filters Nasıl Uygulanır?
- **`OnModelCreating()`** fonksiyonu içerisinde **`HasQueryFilter()`** fonksiyonu ile uygulanır.
```csharp
modelBuilder.Entity<Person>().HasQueryFilter(p => p.IsActive);
```
```csharp
// HasQueryFilter olmadan sorgu.
await context.Persons.Where(p => p.IsActive).ToListAsync();
// HasQueryFilter'lı sorgu
await context.Persons.ToListAsync();
await context.Persons.FirstOrDefaultAsync(p => p.Name.Contains("a") || p.PersonId == 3);
```
### Navigation Property Üzerinde Global Query Filters Kullanımı
```csharp
modelBuilder.Entity<Person>().HasQueryFilter(p => p.Orders.Count > 0);
```
```csharp
// HasQueryFilter olmadan sorgu.
var p1 = await context.Persons
    .AsNoTracking()
    .Include(p => p.Orders)
    .Where(p => p.Orders.Count > 0)
    .ToListAsync();
    
// HasQueryFilter'lı sorgu
var p2 = await context.Persons.AsNoTracking().ToListAsync();
```
### Global Query Filters Nasıl Ignore Edilir? - IgnoreQueryFilters
```csharp
var person2 = await context.Persons.IgnoreQueryFilters().ToListAsync();
```

### Dikkat Edilmesi Gereken Husus!
- Global Query Filter uygulanmış bir kolona farkında olmaksızın şart uygulanabilmektedir. Bu duruma dikkat edilmelidir.
```csharp
await context.Persons.Where(p => p.IsActive).ToListAsync();
```

