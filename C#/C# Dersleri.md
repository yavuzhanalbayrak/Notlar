# C# Dersleri

*C# dilindeki bazı nadir ama faydalı kodlar.*


   ## Notlar
    *Casting işlemi[(int)mesaj, (double)mesaj] --> unboxing, Bilinçli tür dönüşümü, int-char(ASCII) dönüşümü
    C# Dilinde değer türlü değişkenler normal şartlarda null değer alamazlar.
    İki aritmetik değerin işlemi sonucunda sonuç büyük olan türde döner. (int/double=double) İstisna: (byte - byte = int)
    Hata Türleri:
      - Derleme/Syntax/Sözdizimi Hatası
      - Runtime Hataları (Try-Catch)
      - Mantıksal Hatalar*
    
   ## Tür Dönüşümleri
   ```csharp
    //Bilinçsiz Tür Dönüşümü (Küçükten Büyüğe)
    int a = 10;
    float b = a;
    
    //Bilinçli Tür Dönüşümü (Büyükten Küçüğe)
    float d = 3.10F;
    int c = (int)d;
    #endregion
    
    #region @ Operatörü
    string @static = "@ operatörü progmatik keywordleri değişken ismi olarak kullanmayı sağlar";
    ```
   ## $ Operatörü
```csharp
    var sayi1 = 10; //null olammaz çünkü compile aşamasında türü belirlenir.
    dynamic sayi2 = 20; //null olabilir çünkü run time'da türü belirlenir.
    Console.WriteLine($"\t{sayi1} sayısı küçüktür {sayi2}'den");
```
    
## Tuple
```csharp
    (int yas, string isim) kisi = (20, "yavuz");
    
    Console.WriteLine($"\tKişinin yaşı: {kisi.yas}\n" +
                      $"\tKişinin adı:{kisi.isim}");
    #endregion
    ```
    #region Checked (default uncheck'tir)
    //Bilinçli tür dönüşümlerinde eğer ki veri kaybı olursa hata fırlatmaya yarar.
    checked
    {
        int a = 256;
        byte b = (byte)a;   //byte: 0 - 255
        Console.WriteLine(b);
    }
```
   ## Ternary Operatörü 
```csharp
    string mesaj1 = true ? "true" : "false";
    string mesaj2 = false ? "true" : "false";
    string mesaj = 10 > 5 ? "10 sayısı 5'ten büyüktür" : "10 sayısı 5ten küçüktür";
    Console.WriteLine(mesaj);

    //Ternary Operatörü Else if'li yapısı
    int sayi=int.Parse(Console.ReadLine());
    string mesaj = sayi < 5 ? "sayi 5'ten küçük" : sayi <= 10 ? "Sayı 5 ile 10 arasında" : "Sayı 10'dan büyük";
    Console.WriteLine(mesaj);
```    
    
   ## is Operatörü
```csharp
    object x = 5;
    Console.WriteLine(x is int);
    Console.WriteLine(x is not int);
    Console.WriteLine(x is Program);
    Console.WriteLine(x is null);
    Console.WriteLine(x is not null);
```
    
   ## ?(Nullable) Operatörü
```csharp
    int a1 = null;
    int? a2 = null;
    if (a2 is not null) ; //pratik kullanım
``` 
    
   ## ??(Null-Coalescing) && ??=(Null-Coalescing Assignment)
```csharp
    string? a3 = null;
    Console.WriteLine(a3 ?? "A değeri atanmamış");
    Console.WriteLine($"(??)  a: {a}");
    Console.WriteLine(a3 ??= "A değeri atanmamış");
    Console.WriteLine($"(??=) a: {a}");
```
    
   ## in keyword'ü
```csharp
    void X(in double PI = 3.14)
    {
        //PI = 5.2; //Derleme hatası!!!
        Console.WriteLine($"Pi değeri:{PI}");
    }
    X();
```
    
   ## ref keyword'ü
```csharp
    //Örnek 1
    
    int a = 5;
    ref int b = ref a;
    a++;
    Console.WriteLine(b);
    
    //Örnek 2
    
    int a = 5;
    X(a);
    Console.WriteLine(a);
    Y(ref a);
    Console.WriteLine(a);
    
    void X(int a)
    {
        a = 50;
    }
    void Y(ref int a)
    {
        a = 50;
    }
    
    //Örnek 3
    
    //Fonksiyona a'nın değeri gönderildiğinden a'da herhangi bir değişim olmadı.
    int a = 5;
    int b = X(a);
    Console.WriteLine(a);
    Console.WriteLine(b);
    int X(int a)
    {
        a = 50;
        return a;
    }
    
    //Fonksiyona c'nın referansı gönderildiğinden c değeri de değişime uğradı.
    int c = 5;
    int d = Y(ref c);
    Console.WriteLine(c);
    Console.WriteLine(d);
    ref int Y(ref int c)
    {
        c = 50;
        return ref c;
    }

```
   ## out keyword'ü
```csharp
	 void X( int c, out int b, out string d)
     {
         b = 2*c;
         d = "yavuz";
     }

     X( 5, out int _b, out string _d);
     Console.WriteLine(_b);
     Console.WriteLine(_d);
```
    
   ## TryParse
```csharp
    string a = "12s3";
    if (int.TryParse(a, out int r))
    {
        Console.WriteLine(r + 1);
    }
    else
        Console.WriteLine("Lütfen Sayısal Bir Değer Giriniz.");
    Console.WriteLine("Bitti");
    
    //Try-Catch'den farkı programın else bloğunu çalıştırıp devam etmesidir.
    //Fakat try catch'de catch bloğu çalışırsa eğer throw sayesinde program sonlanır.
    //throw'suz try catch gibi çalışır.
    
    try
    {
        Console.WriteLine(int.Parse("12s3") + 1);
    }
    catch (Exception)
    {
        Console.WriteLine("Lütfen Sayısal Bir Değer Giriniz.");
        //throw;
    }
    Console.WriteLine("Bitti");
   ```
