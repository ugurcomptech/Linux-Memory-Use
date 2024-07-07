#  Linuxun Ram Belleği Aşırı Fazla Kullanması

## Disk Önbelleğe Alma Nedir?
Disk önbelleğe alma, işletim sisteminin kullanılmayan RAM'i (belleği) geçici olarak disk okuma/yazma işlemlerini hızlandırmak için kullanmasıdır. Linux işletim sistemi, diskten okunan verileri RAM'de saklayarak daha hızlı erişim sağlar. Bu, özellikle sık kullanılan dosyalar için büyük bir performans artışı sağlar.

## Neden Yapılır?
Disk önbelleğe alma, sistem performansını artırmak için kullanılır. Disk erişimi genellikle RAM erişiminden çok daha yavaştır, bu nedenle verilerin RAM'de saklanması sistemin daha hızlı ve duyarlı olmasını sağlar. Kullanıcıların gözünden bakıldığında, bu teknik belleğin doluymuş gibi görünmesine neden olabilir, ancak aslında bu bellek her an uygulamalara tekrar tahsis edilebilir.

## Uygulamalar Daha Fazla Bellek İsterse Ne Olur?
Eğer çalıştırdığınız uygulamalar daha fazla belleğe ihtiyaç duyarsa, çekirdek (kernel), önbellekteki verileri serbest bırakır ve bu belleği uygulamalara tahsis eder. Bu süreç genellikle oldukça hızlıdır ve kullanıcı tarafından fark edilmez.

## Takasa (Swap) İhtiyacım Var mı?
Çoğu durumda, disk önbelleği nedeniyle ekstra takasa ihtiyaç duymazsınız. Linux işletim sistemi, RAM'in dolması durumunda öncelikle disk önbelleğini serbest bırakır. Yalnızca bu belleğin yeterli olmadığı durumlarda, sistem bellek alanını genişletmek için takasa başvurur.

## Linux'un Disk Önbelleğe Alma Davranışını Nasıl Değiştirebilirim?
Disk önbelleğe alma işlemini tamamen devre dışı bırakmak mümkün değildir ve genellikle istenen bir durum değildir. Ancak, sistemin swap kullanımını ve önbellek boşaltma davranışını ayarlayabilirsiniz.

Belirli durumlarda, RAM'deki önbelleği temizlemek gerekebilir. Örneğin, RAM'in performans testleri yapmak için boş olması istenebilir. Bu durumda, aşağıdaki komut kullanılarak önbellek boşaltılabilir:

```
echo 1 > /proc/sys/vm/drop_caches
echo 2 > /proc/sys/vm/drop_caches
echo 3 > /proc/sys/vm/drop_caches
```

Bu komut, sistemdeki disk önbelleğini temizler ve RAM'inizi serbest bırakır.

## Neden top ve free Az RAM Gösteriyor?
top ve free gibi araçlar RAM kullanımını farklı terimlerle rapor eder. Kullanıcılar, "kullanılan" belleğin uygulamalar tarafından kullanılan bellek olduğunu, "boş" belleğin ise kullanılmayan bellek olduğunu düşünebilirler. Ancak Linux, "kullanılan" bellek terimini daha geniş bir anlamda kullanır; bu, uygulamalar, önbellek ve tamponlar tarafından kullanılan tüm belleği içerir.

"Boş" bellek, hiçbir şekilde kullanılmayan bellek anlamına gelirken, "kullanılabilir" bellek, gerektiğinde uygulamalara tahsis edilebilecek bellek anlamına gelir. Bu nedenle, Linux sisteminde "boş" bellek düşük görünebilir, ancak "kullanılabilir" bellek miktarı genellikle oldukça yüksektir.

Özetle, Linux'un bellek yönetimi oldukça etkili ve kullanıcı dostudur. Disk önbelleğe alma, sistemin performansını artırmak için kullanılan önemli bir tekniktir ve genellikle kullanıcıların müdahale etmesi gerekmez.

-------------------------------------------------------------------------------
| Memory that is                          | You'd call it    | Linux calls it |
|-----------------------------------------|------------------|----------------|
| used by applications                    | Used             | Used           |
| used, but can be made available         | Free (or Available) | Available    |
| not used for anything                   | Free             | Free           |
-------------------------------------------------------------------------------

Bu "bir şey" (kabaca) top ve free'nin "arabellekler" ve "önbelleğe alınmış" olarak adlandırdığı şeydir. Sizin ve Linux'un terminolojisi farklı olduğundan, olmadığınızda ram'inizin düşük olduğunu düşünebilirsiniz.

## Gerçekten Ne Kadar Boş RAM'iniz Olduğunu Nasıl Görebilirsiniz?

Linux sisteminde gerçekten ne kadar kullanılabilir RAM'iniz olduğunu anlamak için free komutunu kullanabilirsiniz. Bu komut, belleğin nasıl kullanıldığını ayrıntılı olarak gösterir.

Örneğin, aşağıdaki komutu çalıştırarak RAM kullanımınızı görebilirsiniz:

```
free -m
```

Çıktı:

```
               total        used        free      shared  buff/cache   available
Mem:           15989         658       14536         105         794       14948
Swap:              0           0           0
```


Bu tablodaki sütunlar şunları ifade eder:

- **total:** Toplam fiziksel RAM miktarı.
- **used:** Şu anda kullanımda olan RAM miktarı.
- **free:** Kullanılmayan RAM miktarı.
- **shared:** Diğer işlemlerle paylaşılan RAM miktarı.
- **buff/cache:** Disk önbelleği ve tamponlar tarafından kullanılan RAM miktarı.
- **available:** Uygulamalar tarafından kullanılabilir olan RAM miktarı.


## Neden "Kullanılabilir" RAM'e Bakmalıyız?
"Free" sütunu sadece anlık olarak kullanılmayan RAM miktarını gösterir. Ancak "available" sütunu, gerektiğinde uygulamalara tahsis edilebilecek toplam RAM miktarını daha doğru bir şekilde gösterir. Örneğin, yukarıdaki çıktıda free sütunu 14536 MB gösterirken available sütunu 14948 MB göstermektedir. Bu, sisteminizin aslında daha fazla kullanılabilir RAM'e sahip olduğunu gösterir.

2014'ten önceki kurulumlarda, "available" sütunu yoktur. Bu durumda, "-/+ buffers/cache" satırındaki "free" sütununa bakmanız gerekirdi.

## RAM Kullanımınızı Anlamak
Eğer sadece "free" sütununa bakarsanız, RAM'inizin %99 dolu olduğunu düşünebilirsiniz. Ancak "available" sütunu size daha doğru bir resim sunar. "Available" sütunu, önbellek ve tamponlar tarafından kullanılan RAM'in gerektiğinde serbest bırakılabileceğini gösterir.

## Ne Zaman Endişelenmeye Başlamalısınız?
Sağlıklı bir Linux sistemi, bir süre çalıştıktan sonra aşağıdaki beklenen ve zararsız davranışları gösterebilir:

### Sağlıklı Bir Sistemin İşaretleri
- **free** bellek miktarı yakın 0 olabilir.
- **available** bellek miktarı (veya "free + buffers/cache") yeterli olmalıdır (örneğin toplam RAM'in %20'si veya daha fazlası).
- **swap used** miktarı sabit kalmalıdır.

### Düşük Bellek Durumunun Uyarı İşaretleri
Gerçekten düşük bellek durumunu anlamak için dikkat etmeniz gereken bazı uyarı işaretleri şunlardır:
- **available** bellek miktarı sıfıra yakınsa.
- **swap used** miktarı artar veya dalgalanırsa.
- `dmesg | grep oom-killer` komutu, OutOfMemory-killer'ın (OOM-killer) çalıştığını gösterirse.

### Özet
- `free` komutu ile RAM kullanımını görebilirsiniz.
- **"available"** sütunu, sisteminizin gerçekten ne kadar kullanılabilir RAM'e sahip olduğunu gösterir.
- Sağlıklı bir sistemde, **"available"** bellek yeterli miktarda olmalıdır ve **"swap used"** miktarı değişmemelidir.
- Gerçekten düşük bellek durumu, **"available"** belleğin sıfıra yakın olması, **"swap used"** miktarının artması veya OOM-killer'ın devreye girmesi ile anlaşılabilir.




