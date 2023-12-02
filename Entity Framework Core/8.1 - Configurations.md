# Configurations
### EF Core'da Neden Yapılandırmalara İhtiyacımız Olur?
Default davranışları yeri geldiğinde geçersiz kılmak ve özelleştirmek isteyebiliriz. Bundan dolayı yapılandırmalara ihtiyacımız olacaktır.

#### OnModelCreating Metodu
- EF Core'da yapılandırma deyince akla ilk gelen metot OnModelCreating metodudur.
- Bu metot, DbContext sınıfı içerisinde virtual olarak ayarlanmış bir metottur.
- Bizler bu metodu kullanarak model'larımızla ilgili temel konfigürasyonel davranışları(Fluent API) sergileyeibliriz.
- Bir model'ın yaratılışıyla ilgili tüm konfigürasyonları burada gerçekleştirebilmekteyiz.

#### GetEntityTypes
- EF Core'da kullanılan entity'leri elde etmek, programatik olarak öğrenmek istiyorsak eğer GetEntityTypes fonksiyonunu kullanabiliriz.
```csharp
#region GetEntityTypes
var entities = modelBuilder.Model.GetEntityTypes();
foreach (var entity in entities)
{
	Console.WriteLine(entity.Name);
}
#endregion
```
### Configurations | Data Annotations & Fluent API:

#### Table - ToTable
- Generate edilecek tablonun ismini belirlememizi sağlayan yapılandırmadır.
- Ef Core normal şartlarda generate edeceği tablonun adını DbSet property'sinden almaktadır. Bizler eğer ki bunu özelleştirmek istiyorsak Table attribute'unu yahut ToTable api'ını kullanabilriiz.

#### Column - HasColumnName, HasColumnType, HasColumnOrder
- EF Core'da tabloların kolonları entity sınıfları içerisindeki property'lere karşılık gelmektedir. 
- Default olarak property'lerin adı kolon adıyken, türleri/tipleri kolon türleridir.
- Eğer ki generate edilecek kolon isimlerine ve türlerine müdahale etmek sitiyorsak bu konfigürasyon kullanılır.

```csharp
#region ToTable
modelBuilder.Entity<Person>().ToTable("aksdmkasmdk");
#endregion
#region Column
modelBuilder.Entity<Person>()
		    .Property(p => p.Name)
		    .HasColumnName("Adi")
		    .HasColumnType("asldalsd")
		    .HasColumnOrder(7);
#endregion
```

#### ForeignKey - HasForeignKey
- İlişkisel tablo tasarımlarında, bağımlı tabloda esas tabloya karşılık gelecek verilerin tutulduğu kolonu foreign key olarak temsil etmekteyiz.
- EF Core'da foreign key kolonu genellikle Entity Tnaımlama kuralları gereği default yapılanmalarla oluşturulur.
- ForeignKey Data Annotations Attribute'unu direkt kullanabilirsiniz. Lakin Fluent api ile bu konfigürasyonu yapacaksanız iki entity arasındaki ilişkiyide modellemeniz gerekmektedir. Aksi taktirde fluent api üzerinde HasForeignKey fonksiyonunu kullanamnazsınız!
```csharp
#region ForeignKey
modelBuilder.Entity<Person>()
		    .HasOne(p => p.Department)
		    .WithMany(d => d.Persons)
		    .HasForeignKey(p => p.DId);
#endregion
```

#### NotMapped - Ignore
- EF Core, entity sınıfları içerisindeki tüm proeprtyleri default olarak modellenen tabloya kolon şeklinde migrate eder.
- Bazen bizler entity sınıfları içerisinde tabloda bir kolona karşılık gelmeyen propertyler tanımlamak mecburiyetinde kalabiliriz.
- Bu property'lerin ef core tarafından kolon olarak map edilmesini istemediğimizi bildirebilmek için NotMapped ya da Ignore kullanabiliriz.
```csharp
#region Ignore
modelBuilder.Entity<Person>()
		    .Ignore(p => p.Laylaylom);
#endregion
```
#### Key - HasKey
- EF Core'da, default convention olarak bir entity'nin içerisinde Id, ID, EntityId, EntityID vs. şeklinde tanımlanan tüm proeprtylere varsayılan olarak primary key constraint uygulanır.
- Key ya da HasKey yapılanmalarıyla istediğinmiz her hangi bir proeprty'e default convention dışında pk uygulayabiliriz.
- EF Core'da bir entity içerisinde kesinlikle PK'i temsil edecek olan property bulunmalıdır. Aksi taktirde EF Core migration olutşurken hata verecektir. Eğer ki tablonun PK'i yoksa bunun bildirilmesi gerekir. 
```csharp
#region Primary Key
modelBuilder.Entity<Person>()
		    .HasKey(p => p.Id);
#endregion
```
#### Timestamp - IsRowVersion
- İleride/sonraki derlerde veri tutarlılığı ile ilgili bir ders yapacağız.
- Bu derste bir satırdaki verinin bütünsel olarak değişikliğini takip etmemizi sağlayacak olan verisyon mantığını konuşuyor olacağız.
- İşte bir verinin verisyonunu oluşturmamızı sağlayan yapılanma bu konfigürasyonlardır.
```csharp
#region IsRowVersion
modelBuilder.Entity<Person>()
		    .Property(p => p.RowVersion)
		    .IsRowVersion();
#endregion
```
#### Required - IsRequired
- Bir kolonun nullable ya da not null olup olmamasını bu konfigürasyonla belirleyebiliriz.
- EF Core'da bir property default oalrak not null şeklinde tanımlanır. Eğer ki property'si nullable yapmak istyorsak türü üzerinde ?(nullable) operatörü ile bbildirimde bulunmamız gerekmektedir.
```csharp
#region Required
modelBuilder.Entity<Person>()
		    .Property(p => p.Surname).IsRequired();
#endregion
```
#### MaxLenght | StringLength - HasMaxLength
- Bir kolonun max karakter sayısını belirlememizi sağlar.
```csharp
#region MaxLength
modelBuilder.Entity<Person>()
		    .Property(p => p.Surname)
		    .HasMaxLength(13);
#endregion
```
#### Precision - HasPrecision
- Küsüratlı sayılarda bir kesinlik belirtmemizi ve noktanın hanesini bildirmemizi sağlayan bir yapılandırmadır.
```csharp
#region Precision
modelBuilder.Entity<Person>()
		    .Property(p => p.Salary)
		    .HasPrecision(5, 3);
#endregion
```
#### Unicode - IsUnicode
- Kolon içerisinde unicode karakterler kullanılacaksa bu yapılandırmadan istifade edilebilir.
```csharp
#region Unicode
modelBuilder.Entity<Person>()
		    .Property(p => p.Surname)
		    .IsUnicode();
#endregion
```
#### Comment - HasComment
- EF Core üzerinden oluşturulmuş olan veritabanı nesneleri üzerinde bir açıkalama/yorum yapmak istiyorsanız Comment'i kullanblirsiniz.
```csharp
#region Comment
modelBuilder.Entity<Person>()
	        .HasComment("Bu tablo şuna yaramaktadır...")
		    .Property(p => p.Surname)
	        .HasComment("Bu kolon şuna yaramaktadır.");
#endregion
```
#### ConcurrencyCheck - IsConcurrencyToken
- İleride/sonraki derlerde veri tutarlılığı ile ilgili bir ders yapacağız.
- Bu derste bir satırdaki verinin bütünsel olarak tutarlılığını sağlayacak bir concurrency token yapılanmasından bahsececeğiz.
```csharp
#region ConcurrencyCheck
modelBuilder.Entity<Person>()
		    .Property(p => p.ConcurrencyCheck)
		    .IsConcurrencyToken();
#endregion
```
#### InverseProperty
- İki entity arasında birden fazla ilişki varsa eğer bu ilişkilerin hangi navigation property üzerinden oılacağını ayarlamamızı sağlayan bir konfigrasyondur.
```csharp
public class Airport
{
    public int AirportID { get; set; }
    public string Name { get; set; }
    [InverseProperty(nameof(Flight.DepartureAirport))]
    public virtual ICollection<Flight> DepartingFlights { get; set; }

    [InverseProperty(nameof(Flight.ArrivalAirport))]
    public virtual ICollection<Flight> ArrivingFlights { get; set; }
}
```
### Configurations | Fluent API:

#### Composite Key
- Tablolarda birden fazla kolonu kümülatif olarak primary key yapmak istiyorsak buna composite key denir.
```csharp
#region CompositeKey
modelBuilder.Entity<Person>().HasKey("Id", "Id2");
modelBuilder.Entity<Person>().HasKey(p => new { p.Id, p.Id2 });
#endregion
```
#### HasDefaultSchema
- EF Core üzerinden inşa edilen herhangi bir veritabanı nesnesi default olarak dbo şemasına sahiptir. Bunu özelleştirebilmek için kullanılan bir yapılandırmadır.
```csharp
#region HasDefaultSchema
modelBuilder.HasDefaultSchema("ahmet");
#endregion
```
### Property:
#### HasDefaultValue
- Tablodaki herhangi bir kolonun değer gönderilmediği durumlarda default olarak hangi değeri alacağını belirler.
```csharp
#region HasDefaultValue
modelBuilder.Entity<Person>()
			.Property(p => p.Salary)
			.HasDefaultValue(100);
#endregion
```
#### HasDefaultValueSql
- Tablodaki herhangi bir kolonun değer gönderilmediği durumlarda default olarak hangi sql cümleciğinden değeri alacağını belirler.
```csharp
#region HasDefaultValueSql
modelBuilder.Entity<Person>()
		    .Property(p => p.CreatedDate)
		    .HasDefaultValueSql("GETDATE()");
#endregion
```

#### HasComputedColumnSql
- Tablolarda birden fazla kolondaki veirleri işleyerek değerini oluşturan kolonlara Computed Column denmektedir. EF Core üzerinden bu tarz computed column oluşturabilmek için kullanıolan bir yapılandırmadır.
```csharp
#region HasComputedColumnSql
modelBuilder.Entity<Example>()
		    .Property(p => p.Computed)
		    .HasComputedColumnSql("[X] + [Y]");
#endregion
```
#### HasConstraintName
- EF Core üzerinden oluşturulkan constraint'lere default isim yerine özelleştirilmiş bir isim verebilmek için kullanılan yapılandırmadır.
```csharp
#region HasConstraintName
modelBuilder.Entity<Person>()
		    .HasOne(p => p.Department)
		    .WithMany(d => d.Persons)
		    .HasForeignKey(p => p.DepartmentId)
		    .HasConstraintName("ahmet");
#endregion
```
#### HasData
- Sonraki derslerimizde Seed Data isimli bir konuyu incleyeceğiz. Bu konuda migrate sürecinde veritabanını inşa ederken bir yandan da yazılım üzerinden hazır veriler oluşturmak istiyorsak eğer buunun yöntemini usulünü inceliyor olacağız.
- İşte HasData konfigürasyonu bu operasyonun yapılandırma ayağıdır.
- HasData ile migrate sürecinde oluşturulacak olan verilerin pk olan id kolonlarına iradeli bir şekilde değerlerin girilmesi zorunludur!
```csharp
#region HasData
modelBuilder.Entity<Department>().HasData(
    new Department()
{
Name = "asd",
Id = 1
});
modelBuilder.Entity<Person>().HasData(
    new Person
{
Id = 1,
DepartmentId = 1,
Name = "ahmet",
Surname = "filanca",
Salary = 100,
CreatedDate = DateTime.Now
},
    new Person
{
Id = 2,
DepartmentId = 1,
Name = "mehmet",
Surname = "filanca",
Salary = 200,
CreatedDate = DateTime.Now
}
    );
#endregion
```
#### HasDiscriminator
- İleride entityler arasında kalıtımsal ilişkilerin olduğu TPT ve TPH isminde konuları inceliyor olacağız. İşte bu konularla ilgili yapılandırmalarımız HasDiscriminator ve HasValue fonksiyonlarıdır.
```csharp
#region HasDiscriminator
modelBuilder.Entity<Entity>()
		    .HasDiscriminator<string>("Ayirici");
#endregion
```
#### HasValue
```csharp
#region HasDiscriminator
modelBuilder.Entity<Entity>()
		    .HasDiscriminator<int>("Ayirici")
		    .HasValue<A>(1)
		    .HasValue<B>(2)
		    .HasValue<Entity>(3);
#endregion
```
#### HasField
- Backing Field özelliğini kullanmamızı sağlayan bir yapılandırmadır.
```csharp
#region HasField
modelBuilder.Entity<Person>()
		    .Property(p => p.Name)
		    .HasField(nameof(Person._name));
#endregion
```
#### HasNoKey
- Normal şartlarda EF Core'da tüm entitylerin bir PK kolonu olmak zorundadır. Eğer ki entity'de pk kolonu olmayacaksa bunun bildirilmesi gerekmektedir! İşte bunun için kullanuılan fonksiyondur.
```csharp
#region HasNoKey
modelBuilder.Entity<Example>()
		    .HasNoKey();
#endregion
```
#### HasIndex
- Sonraki derslerimizde EF Core üzerinden Index yapılanmasını detaylıca inceliyor olacağız.
- Bu ypılanmaya dair konfigürasyonlarımız HasIndex ve Index attribute'dur.
```csharp
#region HasIndex
modelBuilder.Entity<Person>()
		    .HasIndex(p => new { p.Name, p.Surname });
#endregion
```
#### HasQueryFilter
- İleride göreceğimiz Global QUery Filter başlıklı dersimizin yapılandırmasıdır.
- Temeldeki görevi bir entitye karşılık uygulama bazında global bir filtre koymaktır.
```csharp
#region HasQueryFilter
modelBuilder.Entity<Person>()
		    .HasQueryFilter(p => p.CreatedDate.Year == DateTime.Now.Year);
#endregion
```
#### DatabaseGenerated - ValueGeneratedOnAddOrUpdate, ValueGeneratedOnAdd, ValueGeneratedNever


