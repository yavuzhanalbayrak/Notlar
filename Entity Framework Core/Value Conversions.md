# Value Conversions

### Value Conversions Nedir?
- EF Core üzerinden veritabanı ile yapılan sorgulama süreçlerinde veriler üzerinde dönüşümler yapmamızı sağlayan bir özelliktir.
- SELECT sorguları sürecinde gelecek olan veriler üzerinde dönüşüm yapabiliriz.

- UPDATE yahut INSERT sorgularında da yazılım üzerinden veritabanına gönderdiğimiz veriler üzerinde de dönüşümler yapabilir ve böylece fiziksel olarak da verileri manipüle edebiliriz.

### Value Converter Kullanımı Nasıldır?
- Value conversions özelliğini EF Core'da ki Value Converter yapıları tarafından uygulayabilmekteyiz.

### HasConversion
- HasConversion fonksiyonu, en sade haliyle EF Core züerinden value converter özelliği gören bir fonksiyondur.
```csharp
 modelBuilder.Entity<Person>()
            .Property(p => p.Gender)
            .HasConversion(
                //INSERT - UPDATE
                g => g.ToUpper()
            ,
                //SELECT
                g => g == "M" ? "Male" : "Female"
            );
```

### Enum Değerler İle Value Converter Kullanımı

- Normal durumlarda Enum türünde tutulan propertylerin veritabanındaki karşılıkları int olacak şekilde aktarımı gerçekleştirlimektedir. 
- Value converter sayesinde enum türünden olan propertylerinde dönüşümlerini istediğimiz türlere sağlayarak hem ilgili kolonun türünü o türde ayarlayaiblir hem de enum üzerinden çalış sürecinde verisel dönüşümleri ilgili türde sağlayabiliriz.
```csharp
public enum Gender
{
    Male,
    Famele
}
```
```csharp
modelBuilder.Entity<Person>()
           .Property(p => p.Gender2)
           .HasConversion(
               //INSERT - UPDATE
               g => g.ToString()
               //g => (int)g // Enum Default
           ,
               //SELECT
               g => (Gender)Enum.Parse(typeof(Gender), g)
           );
```

### ValueConverter Sınıfı
- ValueConverter sınıfı, verisel dönüşümlerideki çalışmaları/sorumlulukları üstlenebilecek bir sınıftır.
- Yani bu sınıfın instance'ı ile HasConvention fonksiyonun yapılan çalışmaları üstlenebilir ve direkt bu instance'ı ilgili fonksiyona vererek dönüşümsel çalışmalarımızı gerçekleştirebiliiriz.
- İlk generic parametresi orijinal türü içerirken ikincisi ise dönüştürülecek türü belirtir.
```csharp
ValueConverter<Gender, string> converter = new(
             //INSERT - UPDATE
             g => g.ToString()
             ,
             //SELECT
             g => (Gender)Enum.Parse(typeof(Gender), g)
            );
```
```csharp
modelBuilder.Entity<Person>()
			.Property(p => p.Gender2)
	        .HasConversion(converter);
```
### Custom ValueConverter Sınıfı
- EF Core'da verisel dönüşümler için custom olarak converter sınıfları üretebilmekteyiz. Bunun için tek yapılması gereken custom sınıfının ValueConverter sınıfından miras almasını sağlamaktadır.
```csharp
public class GenderConverter : ValueConverter<Gender, string>
{
    public GenderConverter() : base(
        //INSERT - UPDATE
        g => g.ToString()
        ,
        //SELECT
        g => (Gender)Enum.Parse(typeof(Gender), g)
        )
    {
    }
}
```
```csharp
modelBuilder.Entity<Person>()
         .Property(p => p.Gender2)
         .HasConversion<GenderConverter>();
```

### Built-in Converters Yapıları
- EF Core basit dönüşümler için kendi bünyesinde yerleşik convert sınıfları barındırmaktadır.

#### BoolToZeroOneConverter
- bool olan verinin int olarak tutulmasını sağlar.
```csharp
modelBuilder.Entity<Person>()
			.Property(p => p.Married)
			.HasConversion<BoolToZeroOneConverter<int>>();

//ya da direkt aşağıdaki gibi int türünü bildirirsek de aynı davranış söz konusu olacaktır.
modelBuilder.Entity<Person>()
			.Property(p => p.Married)
			.HasConversion<int>();
```        
#### BoolToStringConverter
- bool olan verinin string olarak tutulmasını sağlar.
```csharp
BoolToStringConverter converter = new("Bekar", "Evli");

modelBuilder.Entity<Person>()
			.Property(p => p.Married)
			.HasConversion(converter);
```   
#### BoolToTwoValuesConverter
- bool olan verinin char olarak tutulmasını sağlar.
```csharp
BoolToTwoValuesConverter<char> converter = new('B', 'E');

modelBuilder.Entity<Person>()
			.Property(p => p.Married)
			.HasConversion(converter);
```
- Diğer built-in converters yapılarını aşağıdaki linkten gözlemleyebilirsiniz.
- https://learn.microsoft.com/en-us/ef/core/modeling/value-conversions?tabs=data-annotations#built-in-converters


### İlkel Koleksiyonların Serilizasyonu
- İçerisinde ilkel türlerden olyuşturulmuş koleksiyonları barındıran modelleri migrate etmeye çalıştığımızda hata ile karşılaşmaktayız. 
- Bu hatadan kurtulmak ve ilgili veriye koleksiyondaki verileri serilize ederek işleyebilmek için bu koleksiyonu normal metinsel değerlere dönüştürmemize fırsat veren bir conversion operasyonu gerçekleştirebiliriz. 
```csharp
modelBuilder.Entity<Person>()
            .Property(p => p.Titles)
            .HasConversion(
            //INSERT - UPDATE
            t => JsonSerializer.Serialize(t, (JsonSerializerOptions)null)
            ,
            //SELECT
            t => JsonSerializer.Deserialize<List<string>>(t, (JsonSerializerOptions)null)
            );
```

### .NET 6 - Value Converter For Nullable Fields
- .NET 6'dan önce value converter'lar null değerlerin dönüşümünü desteklememekteydi. .NET 6 ile artık null değerler de dönüştürülebilmektedir.

}
