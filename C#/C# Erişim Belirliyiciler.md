# C# Erişim Belirleyiciler

C# dilinde, bir sınıfın, özelliğin, metotun veya diğer üye türlerinin erişilebilirliğini belirtmek için kullanılan erişim belirleyicileri (access modifiers) vardır. İşte C# dilindeki temel erişim belirleyicileri: 
## Default:
Eğer bir sınıf veya üye için hiçbir erişim belirleyici belirtilmemişse, varsayılan olarak internal erişim belirleyicisi kullanılır (sınıflar için). Ancak alanlar, metotlar ve özellikler için varsayılan erişim belirleyici private'tır.
## public: 
Üye, herhangi bir sınıf veya metot tarafından erişilebilir. Bu, en geniş erişim seviyesidir. 
## private: 
Üye sadece tanımlandığı sınıf içinden erişilebilir. Bu, en sınırlı erişim seviyesidir. 
## protected:
 Üye sadece tanımlandığı sınıf içerisinde veya bu sınıfı miras alan alt sınıflarda erişilebilir.
## internal:
  Üye, sadece tanımlandığı derleme biriminde (assembly) erişilebilir. Diğer bir deyişle, bir assembly içinde tanımlanan tüm sınıflar bu üyeye erişebilir, ancak bu assembly dışında tanımlanan sınıflar erişemez. 
## protected internal:
Üye, ya tanımlandığı derleme biriminde (assembly) veya bu sınıfı miras alan alt sınıflarda erişilebilir. Ancak bu iki koşulun bir kombinasyonu değil, bu koşullardan herhangi biri ile erişim sağlanabilir. 
## private protected:
Bir üye, sadece tanımlandığı sınıf içinde veya aynı derleme birimindeki (assembly) bu sınıfı miras alan alt sınıflarda erişilebilir. Bu erişim belirleyicileri, üyelerin (metotlar, özellikler, alanlar vs.) veya sınıfların erişilebilirliğini kontrol etmek için kullanılır. 


