# View Model

**View model:** Genellikle bir MVC (Model-View-Controller) veya benzeri bir tasarım deseni kullanılarak geliştirilen web uygulamalarında, kullanıcı arayüzü (UI) ile etkileşimde bulunmak için özel olarak oluşturulan bir model türüdür. View model, UI'nin ihtiyaçlarına uygun olarak düzenlenmiş veri yapısını temsil eder ve genellikle bir veya birden fazla gerçek veri modelini içerir.

View modelin temel amaçları şunlar olabilir:

1.  **UI İhtiyaçlarına Uyum:** View model, kullanıcı arayüzünde görünen verileri ve bu verilere ait özel işlemleri içerir. Bu, UI'nin ihtiyaçlarına uygun bir şekilde verilerin düzenlenmesini sağlar.
    
2.  **Veri Sunumunu Kolaylaştırma:** Gerçek veri modelleri genellikle veritabanındaki tablolara karşılık gelir ve uygulamanın iş mantığı ile daha fazla ilgilidir. View model, bu veri modellerinden gelen verileri, UI'nin ihtiyaçlarına uyacak şekilde düzenleyerek sunmayı amaçlar.
    
3.  **İş Mantığı ve Gösterim Mantığını Ayırma:** View model, iş mantığı ve gösterim mantığını birbirinden ayırmak için kullanılır. Bu, bir uygulamanın bakımını ve genişletilmesini kolaylaştırabilir çünkü UI değişiklikleri, iş mantığı değişikliklerinden bağımsız olarak yapılabilir.
    
4.  **İsteğe Bağlı Alanlar Eklemek:** Bazı durumlarda, UI'de gösterilmeyen veya kullanıcının düzenleme yetkisine sahip olmadığı ancak iş mantığı için gerekli olan alanları içerebilir.

5. **Formdan Gönderilen Parametreleri Yakalamak:** MVC (Model-View-Controller) tasarım deseni kullanılarak geliştirilmiş web uygulamalarında oldukça yaygın bir pratiktir. Bu, birinci sınıf nesneler (view model) kullanarak formdan gelen verileri daha düzenli bir şekilde ele almaya olanak tanır.
    

Örneğin, bir blog uygulaması düşünelim. Gerçek veri modeli `Makale` olabilir, ancak UI'da gösterilen sayfa için sadece makale başlığı, içeriği ve yazar adı gerekebilir. Bu durumda, View model sadece bu üç alanı içerebilir ve UI için uygun bir yapı sağlar.


```csharp
public class MakaleViewModel
{
    public string Baslik { get; set; }
    public string Icerik { get; set; }
    public string YazarAdi { get; set; }
}
``` 

Bu sayede, UI, sadece ihtiyaç duyduğu verilere erişebilir ve bu verilere özgü işlemleri gerçekleştirebilir. Bu aynı zamanda, UI ile veritabanı arasındaki bağımlılığı azaltarak kodun daha esnek ve bakımı kolay olmasına katkı sağlar.
