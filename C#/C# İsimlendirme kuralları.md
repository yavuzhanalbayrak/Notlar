# C# İsimlendirme Kuralları 
*C# programlama dilinde isimlendirme konusunda belirli endüstri standartları ve Microsoft'un önerdiği yönergeler bulunmaktadır. Bu yönergeler, kodun okunabilirliğini ve anlaşılabilirliğini arttırmayı amaçlar. İşte C# için bazı yaygın isimlendirme kuralları:*

1.  **PascalCasing**: İlk harfi büyük olan kelimelerden oluşan isimlendirme yöntemi. Sınıf isimleri ve özellik isimleri için kullanılır.
    -   Örnek: `CustomerAccount`, `MaximumValue`.

2.  **camelCasing**: İlk harfi küçük olan kelimelerden oluşan isimlendirme yöntemi. Yerel değişkenler ve parametreler için kullanılır.
    -   Örnek: `customerAccount`, `maximumValue`.

3.  **_underscorePrefix**: Özel alanlarda (private fields) bazen bir alt çizgi ile başlatılabilir. Ancak bu yaklaşım bazı geliştiriciler tarafından tercih edilmez.
    -   Örnek: `_customerName`, `_id`.

4.  **Interfaces**: İnterface isimleri genellikle "I" harfi ile başlar.
    -   Örnek: `IDisposable`, `IEnumerable`.

5.  **Constants**: Sabitler genellikle PascalCasing kullanılarak isimlendirilir.
    -   Örnek: `MaximumSize`, `DefaultColor`.

6.  **Methods (Metotlar)**: Metot isimleri genellikle PascalCasing kullanılarak isimlendirilir.
    -   Örnek: `CalculateTotal()`, `GetUserName()`.

7.  **Delegates ve Events**: Bu türler için de genellikle PascalCasing kullanılır.
    -   Örnek: `EventHandler`, `ClickEvent`.

8.  **Enum**: Enumlar ve onların değerleri için de PascalCasing kullanılır.
    -   Örnek:
        
        `public enum Days
        {
            Sunday,
            Monday,
            // ...
        }`
