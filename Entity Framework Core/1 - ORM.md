
# ORM
**ORM:** Object-Relational Mapping (Nesne-İlişkisel Eşleme) anlamına gelir. ORM, bir yazılım uygulamasındaki nesne modelini ve veri tabanındaki ilişkisel modeli (örneğin, SQL tablolarını) birbirine bağlama sürecidir. Bu, uygulamanın nesne yönelimli programlamadan (OOP) veri tabanına veya veri tabanından nesne yönelimli programlamaya geçişini kolaylaştıran bir tekniktir.

Temel amaç, programcılara uygulama içinde nesnelerle çalışırken, bu nesnelerin veri tabanındaki karşılıklarını düşünmelerine gerek kalmadan, doğrudan nesne yönelimli bir yaklaşımla veri tabanı işlemleri yapabilmelerini sağlamaktır. Bu sayede, kodun daha okunabilir, sürdürülebilir ve esnek olması hedeflenir.

Bir ORM sistemi, genellikle aşağıdaki temel bileşenleri içerir:

1. **Nesne Modeli (Object Model):** Uygulamanın içinde kullanılan nesneleri ve bu nesneler arasındaki ilişkileri temsil eder. Bu nesne modeli genellikle programlama dilindeki sınıflar veya nesnelerdir.

2. **Veri Modeli (Data Model):** Veri tabanındaki tabloları, sütunları ve ilişkileri temsil eder. Bu veri modeli genellikle SQL veya başka bir veri tabanı sorgu dili ile ifade edilir.

3. **Eşleme (Mapping) Sınıfları:** Nesne modeli ile veri modeli arasındaki eşleştirmeyi sağlar. Bu eşleme sınıfları, nesne örneklerini veritabanındaki kayıtlarla eşleştirmek ve tersine çevirmek için kullanılır.

4. **CRUD Operasyonları (Create, Read, Update, Delete):** Temel veritabanı işlemlerini (ekleme, okuma, güncelleme, silme) gerçekleştirmek için kullanılan yöntemleri içerir.

5. **Sorgu Dili ve Optimizasyon:** ORM, nesne modeli üzerinde yapılan sorguların veritabanına çevrilmesi ve optimize edilmesi işlevselliğini içerir. Bu, uygulamanın performansını artırmak için önemlidir.

6. **İlişki Yönetimi:** İlişkisel veritabanlarında bulunan ilişkileri yönetme yeteneğini içerir. Örneğin, birçoktan çoklu ilişkileri veya birçoktan bir ilişkilerini destekler.

Popüler bir ORM örneği Entity Framework (EF) olarak verilebilir. EF, .NET platformu için bir ORM çerçevesidir ve bu açıklamaların çoğunu sağlar. Diğer programlama dilleri ve platformlar için de birçok ORM çerçevesi bulunmaktadır.
