# Sequences
### Sequence Nedir?
- Veritabanında benzersiz ve ardışık sayısal değerler üreten veritabanı nesnesidir.
- Sequence herhangi bir tablonun özelliği değildir. Veritabanı nesnesidir. Birden fazla tablo tarafından kullanılabilir.

### Sequence Tanımlama
- Sequence'ler üzerinden değer oluştururken veritabanına özgü çalışma yapılması zaruridir. SQL Server'a özel yazılan Sequence tanımı misal olarak Oracle için hata verebilir.

#### HasSequence
- Sequence oluşturmaya yarar.
```csharp
modelBuilder.HasSequence("EC_Sequence");
```

#### HasDefaultValueSql
- Property'e Default olarak değer atamayı sağlar.
- Bu değerleri Sequence'ten çeker. 
```csharp
modelBuilder.Entity<Employee>()
            .Property(e => e.Id)
            .HasDefaultValueSql("NEXT VALUE FOR EC_Sequence");
```

### Sequence Yapılandırması

#### StartsAt:
- Başlangıç değerini ayarlar.
```csharp
modelBuilder.HasSequence("EC_Sequence")
            .StartsAt(100);
```
#### IncrementsBy:
- Artış miktarını ayarlar.
```csharp
modelBuilder.HasSequence("EC_Sequence")
            .IncrementsBy(5);
```
### Sequence İle Identity Farkı
- Sequence bir veritabanı nesnesiyken, Identity ise tabloların özellikleridir.
- Yani Sequence herhangi bir tabloya bağımlı değildir. 
Identity bir sonraki değeri diskten alırken Sequence ise RAM'den alır. Bu yüzden önemli ölçüde Identity'e nazaran daha hızlı, performanslı ve az maliyetlidir.


