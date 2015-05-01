---
layout: post
title: OFFLINE SÖZLÜK UYGULAMASI
---

Bu uygulamanın gerçekleşmesi için yararlanacağımız araçlardan biri veriyi saklama yollarından biri olan ve Android SDK tarafından uygulamalarımızda kullanabilmemiz için sunulan `SQLite` veritabanı kütüphanesidir. `SQLite` kullanılarak kendi uygulamanız için özel relational veritabanı tasarımı yapılabilir; okuma, yazma, güncelleme, silme işlemleri bu veritabanı kullanılarak yönetilebilir. SQLite C programlama dili ile yazılmış, açık kaynak kodlu bir ilişkisel veritabanıdır. Diğer veritabanlarından farkı `serverless` olmasıdır. Android uygulamanız içerisinde, farklı bir process gerektirmeksizin çalışan, gömülü bir veritabanı sistemidir.

Bir kütüphane olarak projeye eklenebiliyor olması ve uygulamanın bir parçası olarak klasik disk sürücüleri üzerinde veri yönetimi gerçekleştirmesi sebebiyle ciddi bir kullanıcı kitlesi vardır.

Veritabanı oluşturmak için en iyi yöntem ***SQLiteOpenHelper*** sınıfından türeyen yeni bir sınıf oluşturmaktır. ***SQLiteOpenHelper*** sınıfını kullandığınızda database oluşturma ve güncelleme işlemlerinin doğru zamanlarda ve doğru şekilde yapılmasını sisteme devretmiş olursunuz.

Bu sınıfın sıkça kullanılan metodu aşağıdaki gibidir:

     onCreate (SQLiteDatabase db)
	
***onCreate***, veritabanı ilk oluşturulduğunda çağrılan bir fonksiyondur. Burada tablolarımızı oluşturma işlemini yapabiliriz.

**1. Sözlük Veritabanı ve Kelime Tablosunun Oluşturulması**

![](http://i.hizliresim.com/zAm8Dg.png)

•	15 – 20. satır arasında veritabanı ve tablo oluşturma işlemleri yapıldı. Tabloda unique bir id kolonu bulunması için autoincrement (otomatik olarak arttırılan) deyimi kullanılarak bir kolon tanımlanmıştır.

•	21. satırda veritabanı güncellemelerinde kullanılabilcek olan tablo silme cümlesini içeren bir sabit tanımı yapılmıştır.

•	24. satırda, veritabanı adını ve versiyonunu içinde bulunduran bir constructor tanımı yapılmıştır.

•	30. satır daha önce de söylediğimiz gibi, veritabanı ilk oluşturulduğu zaman çağırılacaktır. Burada kelime tablosunu oluşturacak olan ***DATABASE_CREATE***  sql ifadesi, ***SQLiteDatabase*** sınıfınınv` execSQL` metoduna argüman olarak verilmiştir. Bu metod, sql ifadesinin ilgili veritabanında çalıştırılmasını sağlayacaktır.

![](http://i.hizliresim.com/rQgWzB.png)

**2. Kelime Ekleme ve Sorgulama İşlemi**

![](http://i.hizliresim.com/LapkW1.jpg)

![](http://i.hizliresim.com/XvBVzR.jpg)

•	26. satıra kadar olan bölümde veritabanı adı, tablo adı, kolon isimleri ve veritabanı versiyonu için birer static sabit tanımı yapıldı. Bu tanımlamalar DatabaseInitializer sınıfında tablo oluşturulurken kullanılmıştır.

•	DatabaseInitializer tipinde argüman alan bir constructor tanımı yapıldı ve en güncel initializer sınıfı üzerinden veritabanına `getWritableDatabase` veya `getReadableDatabase` metodları ile erişim sağlandı.

•	`getWritableDatabase` fonksiyonu yazılabilir veritabanı referansını döndürecektir. `getReadableDatabase` metodu da benzer şekilde okunabilir veritabanı referansını verir. Bu referanslar üzerinden database üzerinde ***okuma***(select) ve ***yazma***(insert, update) işlemleri yerine getirilebilir.

•	Bu metodlar çağırıldığında initializer sınıfı, eğer veritabanı henüz oluşturulmamışsa otomatik olarak oluşturulacaktır, oluşturulmuşsa mevcut referansı döndürür. Böylece bu kontrolleri tek tek bizim yapmamıza gerek kalmaz. Yine performans sebebiyle her seferinde tekrardan veritabanı bağlantısı kurmak yerine, sistem otomatik olarak bağlantınızı cacheleyecektir. Bu sayede okuma, yazma işlemlerinde tekrardan bağlantı kurmaya gerek kalmaz. Fakat, artık ihtiyaç duymayacağınızdan emin olduğunuz zamanda `db.close(`) fonksiyonu ile bağlantının sonlandığından emin olunmalıdır.

•	Veritabanı ve gerekli tablolar oluşturulduktan sonra artık sıra bu tablo üzerinde yazma ve okuma işlemi yapmaya gelir. Bunların yapılabilmesi için ***Cursor*** sınıfından yararlanılmıştır. `Cursor`, sorgulama sonucunda dönen sonuç kümesini temsil eden bir işaretçidir. Yapılan sorgunun sonucunda bir milyon kayıt dönmesi söz konusuysa, bu kadar kaydı içeren bir liste döndürmek performans açısından doğru değildir. Bu nedenle kayıtlaın tamamını elde etmek yerine, kayıtlara işaret eden bir referans kullanılır. Cursor ile kayıtlar üzerinde tek tek gezinebilir, kayıtların başına veya sonuna kolayca ulaşılabilir.

•	36 – 43. satırları arasında tabloya kelime ve anlamının eklenmesi işlemi yapılmıştır.

Bunun için öncelikle getWritableDatabase fonksiyonu yardımıyla yazılabilir veritabanının referansına ulaşılmıştır. 39. Satırda tabloya kelime eklenmesini sağlayan sql cümleciği tanımlanmıştır ve `execSQL`  ile ilgili veritabanında çalıştırılması sağlanmıştır.

• 46 ve 77.  satırlar arasında ve benzer olarak 78-109. satır ile 150-178. satır arasında tabloya eklenilen kelimelerin kolonlara göre yani aranılan kelimeye ve tipine göre sorgulanma işlemi yapılmıştır. Veritabanında sorgulama yapılacağı için sql cümlesi select deyimi içermelidir.  İngilizce kelime sorgusu için ENGLISH, Türkçe kelime sorgusu için ise daha önceden tanımlanmış olan TURKISH kolon adı kullanılmalıdır. Tüm kolonların sorgulanması için select sorgusunda  koymak "*" yeterlidir. Daha sonra getReadableDatabase ile okunabilir database referansına ulaşılmalıdır. Çünkü sql sorgumuz bu database referansı üzerinde çalışacaktır. 

    db.rawQuery(sql, new String[]{englishWord+ ”%” });
	
ile sql cümleciğinde yer alan “?” işareti yerine `englishWord` argümanı geçirilerek initializer sınıfı tipindeki databasede sorgulama işlemi gerçekleştirilir. Sorgu sonucu beklenildiği üzere `Cursor` tipindedir.

    cursor.moveToNext();	

satırında, sorgulama sonucunda cursor işaretçisi listedeki elemanlardan ilkinin bir adım gerisinde durur. Yani ilk elemanı işaret etmez. Ancak moveToNext metodunu çağırdığınızda ilk elemana erişmiş olursunuz. `moveToNext` metodu başarıyla işaretçiyi hareket ettirirse ***true*** döner.

Cursor işaretçisinde aradığımızı bulabilmek için, bu sınıfın sunduğu bazı metodlardan faydalanılmalıdır. Bunlardan birkaçı `getInt()` ve `getString()` metodlarıdır. Cursorde bulunan Integer ve String tipteki değerlere ulaşabilmek için bu fonksiyonlardan faydalanırız. Tablomuzun 4 kolonunun ilk kısmında int tipinde bir id değişkeni tutulduğundan bu değere ulaşabilmek için getInt(0) metodu, String tipindeki diğer değişkenlere ulaşabilmek için tablodaki diziliş konumlarına göre (örneğin ENGLISH kolonu 1. Kolonda, TURKISH kolonu 2. Kolondadır.) getString(1) yani bir başka deyişle “bana 1. Kolonda bulunan String değerini getir.” metodları kullanıldı. Elde edilen bu değerlere daha sonradan ulaşabilmek amacıyla bu değişkenler Bean constructoruna argüman olarak geçirilerak bir nesne oluşturulmuş ve Bean sınıfı tipinde nesne kabul eden ArrayList sınıfına wordList referansında add() metodu ile eklenmiştir.
-ArrayListten alınacak olan veri tipi Object sınıfı tipinde olacaktır.-

**3. UI Events – Activity Sınıfı**

![](http://i.hizliresim.com/5d8Pjj.png)

ListActivity sınıfından extend edilen Dictionary sınıfı genel olarak; input alanları(editText)na kelime girildiğinde, butona tıklanıldığında ya da MenuItem objelerine tıklanıldığında gerçekleşecek işlemlerin yer aldığı `Launcher Activity` sınıftır.

`main.xml` layout dosyasında yer alan ve aranılacak kelimelerin girileceği  input id’li editText alanını ve kelimenin karşılığının çıktı olarak gösterileceği empty id’li TextView alanını sınıfımızda kullanabilmek için input ve empty referansıyla tanımlandı.

Input alanına kelime girilmeye başlanıldığı anda, değişikliklerin takip edilebilmesi için ***TextWatcher*** sınıfından bir Listener ile event takibi sağlandı. Kelime girildiğinde `loadData()` fonksiyonu çağrıldı. Peki loadData fonksiyonunun işlevi nedir?

![](http://i.hizliresim.com/g5kdNO.png)

LoadData fonksiyonu ile girilen kelimenin aranması işlemi AsyncTask yardımı ile thread olarak arka planda çalışır. AsyncTask, android uygulamalarında uzun sürecek işlemlerin arka planda yapılmasının sebebi ana akışın (Main Thread ya da UI Thread) uzun süre engellenmemesidir. Eğer ana akışı uzun süre bloke ederseniz uygulamanızın ana ekranı donacak ve kullanıcı uygulamanızın bozulduğunu düşünecektir. AsyncTask ile arama işlemi arka planda ana akışa etkisi olmayacak  şekilde  gerçekleşir.  Bu  işlem  arka  planda  devam  ederken uygulamada  başka işlemler yapılabilmesine de olanak sağlar. Bu işlem, girilecek kelimenin Türkçe ya da İngilizce olduğunu anlayacak bir mekanizma tasarlamadığımdan loadData fonksiyonunun her çağırılmasında tek kelime için 2 thread çalışacak ve kendisinde bu kelimenin varlığını kontrol edecektir. 

![](http://i.hizliresim.com/EYV7ng.png)

Menu klasörü altında bulunan `menu.xml` dosyasında da tanımlanmış olan MenuItem ikonlarından herhangi birine tıklanıldığında gerçekleşecek olaylar `onOptionsItemSelected()` metodu altında tanımlanmıştır. Seçilen itemlerin menu.xml dosyasında tanımlanmış oldukları id değeriyle eşleşmesi durumunda istenilen durum gerçekleştirilebilir. Bunun kontrolü ise `item.getItemId()` fonksiyonu ile yapılır.

***3.1. Kelime Ekleme***

Kelime ekleme işlevi `showInputDialog()` fonksiyonu içerisinde tanımlanmıştır. Launcher Activityde plus ikonuna tıklanıldığında bu fonksiyon çalışmaya başlar.

***Layout Inflater***, bir XML kaynak dosyasını (layout barındırmasa da olur, hazır veya kendi oluşturduğunuz/özelleştirdiğiniz  bir  View  nesnesine  de  ait  olabilir)  alıp,  onu  bir  View nesnesine çevirmeye ve üzerinde bu yolla işlem yapmaya imkan sağlayan bir sınıftır. Haliyle View objeleri üzerinde alışılmışın üstünde işlemler yapıldığında (Activity'nin onCreate(), onResume() gibi zamanlarında değil de, daha çok kullanıcıyla etkileşim sırasında, değişen bir şarta   göre   -çalışma   zamanında-   yapılacak   değişiklikler),onları   bir   ViewGroup   altında kullanacak  olunduğunda  vs.  kullanılır.  Bir  view  içine  başka  bir  view koyarak  ona  şekil verebilirsiniz. Biz de dialog ekranında tam olarak bunu yaptık. `add_new.xml` dosyası layout inflater aracılığıyla dialog penceresi içerisine `setView()` fonksiyonu ile yerleştirildi. Dialog penceresinde Save butonuna basıldığında öncelikle input alanlarının boş olup olmadığının kontrolü yapıldı. Alanların boş olmadığı durumlarda girilen kelimeler  dictionaryDB sınıfının `addWord()` fonksiyonuna geçirilir ve kelime ekleme işlemi başarılı bir şekilde gerçekleşmiş olur. Cancel butonu ise dialog penceresinde yapılan tüm işlemleri iptal ederek, geçersiz kılar.

![](http://i.hizliresim.com/g5kNl0.png)

**4. Databasede Yer Alan Tüm Kelimelerin Randomize Olarak Schedule Edilmesi** 
 
 Bir Android sistem servisi olan ***AlarmManager*** kullanarak zaman ile ilgili uyarıları gerçekleştirebilir ve ***PendingIntent*** kullanarak o an uygulamamız kapalı olsa bile başka bir servisi, aktiviteyi yada broadcast yayınını kendi haklarımızla başlatabiliriz. PendingIntent’ler genelde AlarmManager yada NotificationManager Android sistem servisleri ile birlikte kullanılırlar. 

![](http://i.hizliresim.com/ogALzR.png)

![](http://i.hizliresim.com/QARk4v.png)


***Send Notification*** butonuna basıldığında,  setTime fonksiyonu devreye girer ve kullanıcıdan notificationın hangi zaman aralıklarında gelmesi gerektiğinin bilgisini alır. Bunun için kelime ekleme işlemi için de kullanılan dialog pencereleri kullanılarak arayüz sağlanmıştır. Layout inflater aracılığıyla `notification_add.xml` dosyası  dialog penceresi içerisine setView() fonksiyonu ile yerleştirildi. Dialog penceresinde ***Save*** butonuna basıldığında öncelikle input alanının boş olup olmadığının kontrolü yapıldı. Alanların boş olmadığı durumlarda girilen süre createNotification fonksiyonuna argüman olarak geçirilir. Cancel butonu ise dialog penceresinde yapılan tüm işlemleri iptal ederek, geçersiz kılar.

`createNotification` metodunda ise pending intent oluşturularak alarm servisine teslim edilir ve böylece sanki kullanıcı başlatıyormuş gibi, alarm servisi daha sonra uygulama kapansa dahi aynı haklarla intenti devreye sokar.

`createNotification` metodunda bulunan bu satırda, intent yardımı ile Broadcast bildirimi yapılmıştır. Broadcastlar intentler halinde bildirilirler. 

Alarmlarla işlem tetiklemenin tavsiye edilen yöntemi ***Broadcast***(yayın) yapmaktır. Servis veya aktiviteyi başlatacağınız tarafta da bir ***Broadcast Receiver*** kullanarak gerekli komponenti başlatmasını sağlamaktır.

Alarm oluşturmanın ilk aşaması, alarm servisini yöneten  AlarmManager’a erişmektir. İkinci aşama ise alarmın tipine karar vermektir. Uygulamamızda bu tip ***RTC*** olarak belirlenmiştir. RTC ile cihaz uyku modunda ise, alarm tetiklendiğinde uyandırılmaz. İşlem için cihazın uyanması beklenir.

Alarmın tipine karar verildikten sonra yapılması gereken 3. şey; alarmın ne zaman tetikleneceğine karar vermektir. Alarm oluşturmanın bir sonraki aşaması; yapılan yayını dinleyecek bir Broadcast receiver oluşturmaktır.

![](http://i.hizliresim.com/GYvqEN.png)

Gönderilen mesajı dinleyen bir yayın algılayıcı oluşturulduktan sonra sıra gönderilecek mesajın kendisini, yani Intent nesnesini oluşturmaya gelir.


     Intent alarmIntent = new Intent(this, AlarmReceiver.class);	

Burada `AlarmReceiver` sınıfını işaret eden bir Intent nesnesi oluşturuldu. Intent oluşturduktan sonra, alarmı kurmadan önce yapmanız gereken son şey, bir ***PendingIntent*** nesnesi tanımlamaktır.

***Intent***, aynı uygulama içerisinde bir aktivitenin ya da yayının tetiklenmesini sağlayan bir mesajı temsil eder. ***Pending Intent*** de farklı uygulamalara gönderilen, sizin yerinize yapılmasını istediğiniz işlemleri (intent) içeren yapılardır. AlarmManager uygulamanızdan bağımsız bir servistir. Yukarıda AlarmReceiver ile yayın algılayıcısını işaret eden bir intent oluşturarak, ne yapmak istediğimiz belirtilir. Bu mesaj da  pendingIntent içerisine yerleştirilir. Böyle yaparak Alarmları yöneten servise “*Bak benim böyle bir intentim var. Lütfen bunu zamanı geldiğinde çalıştır.*” demiş olursunuz.

`getBroadcast`  metodunun ilk argümanı contexti gösterir. İkinci argüman bir requestCode değeridir. Gönderenleri ayırt etmek için kullanılır. 3. Argüman, intent nesnesidir. Son argüman Intent Flag değeridir. Önemli değilse 0 gönderebilirsiniz.

Bu kod çalıştırıldıktan sonra alarm tetiklenecek ve AlarmReceiver için bir broadcast yayın yapılacaktır.

***BroadCastReceiver*** işletim sistemi tarafından uygulamalara gönderilen sinyalleri dinlemek ve takip etmekle görevlidir. İşletim sistemi genelinde olaylardan haberdar olmamızı ve bu olaylar gerçekleştiğinde tepki verilebilmesine olanak sağlar. Bu sinyaller işletim sistemiyle birlikte gelen sistem uygulamaları tarafından oluşturulabileceği gibi market üzerinden indirilen uygulamalar tarafından da gönderilebilir. Bir arama gerçekleştiğinde, SMS ulaştığında, telefon açıldığında veya kapanırken, kulaklık takıldığında ve bunun gibi birçok durumda işletim sistemi olayın gerçekleştiğine dair bir broadcast yapar. Haberdar olmak isteyen uygulamalar, bu yayınlara kayıt olarak istedikleri işlemleri gerçekleştirebilirler.

Bir nesneye ***BroadCastReceiver*** özelliği eklememiz için ilk olarak o nesneyi ***AndroidManifest*** dosyasında kaydetmemiz gerekiyor. Bu şekilde uygulamamız işletim sisteminin gönderdiği sinyalleri alma yetkisi kazanacak.

Aşağıdaki örnekte `AlarmReceiver` adında bir BroadCastReceiver sınıfı oluşturduk. İşletim sistemi bir uyarı yayınladığında BroadCastReceiver içindeki `onReceive `metodu harekete geçer. Bu şekilde gelen uyarı her ne ise bu metot içinde uyarıyla ilgili eylem gerçekleştirme şansımız olur.  Bu sınıfın amacı Send Notification butonuna basıldığında harekete geçerek Databasedeki tüm kelimeleri okur ve array adındaki ArrayList dizisine `add(`) metodu ile tek tek ekleme eylemini gerçekleştirmek olacak.

BroadcastReceiver sınıfını oluşturmamazın asıl amacı, gelen uyarıyı yakalayarak servis yardımı ile kelime hatırlatma işlemlerinin 1 kez değil de arka planda sürekli çalışarak ve düzenli aralıklarla bildirilmesini  sağlamaktır. 

Android uygulamalarında arka planda çalışan ve kullanıcının uygulamayla olan ilişkisini etkilemeyen işlemler için ***Service*** adında bir sınıf bulunur. Bir servis başlatıldıktan sonra, hangi uygulama açık olursa olsun çalışmaya devam edecektir. Veritabanı güncellemeleri, günlük hata raporlarının bildirilmesi gibi rutin arka plan işlemleri için bir servis yaratma ihtiyacı duyduğumuzda ***Service*** sınıfından bir alt sınıf yaratarak gerekli işlemleri kullanıcıyı rahatsız etmeden gerçekleştirebiliriz.

       Intent service = new Intent(context, AlarmService.class);
	
`onReceive` altında `startService` ile yeni bir servis tetikleyebiliriz. Burada servise gönderilen Intent içinde `putExtra` metoduyla servisin içeriğini barındıracağı array dizisini argüman olarak veriyoruz. ***AlarmService*** sınıfı tetikleniyor ve kelimelerin kullanıcının girdiği süreye göre düzenli aralıklarla Notification.Builder aracılığıyla ekranda çıktı olarak gösteriyor.

***AlarmService.java***

![](http://i.hizliresim.com/l7ko7E.png)
![](http://i.hizliresim.com/ogALZ7.png)

***AndroidManifest.xml*** : Application elemanının içerisinde bir receiver tanımlanmıştır. İçerisinde dinlemek istediğimiz broadcast  olan AlarmReceiver sınıfını belirtiyoruz. Bu yaptığımız şey; sisteme uygulamamızın belirtilen yayını takip ettiğini ve bir Broadcast gönderildiğinde AlarmReceiver sınıfının onReceive metodunun çağırılması gerektiğini söylemiş oluyor. Aynı şekilde servis tanımı da yapılmıştır.

![](http://i.hizliresim.com/d5n60Q.png)

**BEHIND THE SCENES**

***1. WordListAdapter.java***

![](http://i.hizliresim.com/AmAo57.png)
![](http://i.hizliresim.com/5d8A7M.png)
![](http://i.hizliresim.com/94V6LQ.png)

ListView nesnesi, ***TextView***, ***EditText*** ya da ***Button*** nesneleri gibi basit bir ***View*** nesnesi değil,***LinearLayout***, ***GridView*** gibi taşıyıcı olarak görev yapan bir View nesnesidir. Bunun anlamı kendi içinde başka View'ları barındırıyor olduğudur. ListView, her satırında bir TextView bulundurur. Bu sıradan bir ListView için geçerli. İsterseniz her satırında daha farklı bir View düzeni kurabilirsiniz.

Bu bölümde kendi düzenimize ve tasarımımıza uygun, ***custom*** (özel) bir ListView oluşturuldu.

    Takip edeceğimiz adımlar şunlardır:

1.	Ana yerleşimi tasarlamak
2.	Satır yerleşimini tasarlamak
3.	Veri modelini oluşturmak
4.	Listede gösterilecek verileri oluşturmak
5.	Özel bir Adapter oluşturmak
6.	Listeyle adaptörü bağlamak

***Ana yerleşimi tasarlamak***
İlk olarak yeni bir proje oluşturalım ve `Dictionary.java` dosyasını ve buna ait `main.xml` isimli layout dosyasını düzenleyelim. Dictionary'nin arayüzünü bir LinearLayout olarak gösterelim ve içine de liste id'sini taşıyan bir ListView ekleyelim:

![](http://i.hizliresim.com/WnVlbL.png)

Bu kadarını tamamladığımz layout şöyle olacaktır: 

![](http://i.hizliresim.com/YBv1O6.png)

Gördüğünüz gibi özelleştirilmemiş bir liste oluştu. Java kodu tarafında listeye erişebilmek için bu öğenin `android:id` özniteliğindeki liste değerini kullanacağız.

***Satır yerleşimini tasarlamak***

Listedeki satırda TextView ile beraber bir ImageView göstereceğiz. Şunun gibi:

![](http://i.hizliresim.com/7koO65.jpg)

Bu görünümü sağlayacak `word.xml` yerleşim dosyası şöyledir:

![](http://i.hizliresim.com/4gLdjG.png)

Birazdan bu `satir_layout.xml` dosyasını, gösterilecek listenin birer satırı olacak şekilde kullanacağız. Bunun için bir inflating yani (dilimize çevirmek gerekirse) şişirme işlemine başvuracağız. 

***Veri modelini oluşturmak***

Özel ListView'lar oluşturmadaki önemli bir husus, liste satırlarında gösterilecek verilerdir.
 
Şimdiki senaryoda verilerimizi, kendi oluşturduğumuz (`main.xml`'deki liste) bir listede, kendi oluşturduğumuz bir düzende (Bkz: `word.xml` dosyası) göstereceğiz. Bu sebeple kendi Adapter'ımızı yazmak zorundayız. Bir Adapter ile uygun verileri, uygun yerleşimlere bağlayabiliyoruz. 

Sol tarafta İngilizce ve karşılığı olan Türkçe kelimeler olacak ve yanında da bookmark simgesi yer alacak. Bu iki veriyi temsil eden bir sınıf yazmalıyız. Nesneye yönelik programlama yaklaşımında böyle sınıflara ***model sınıfları*** da denir. Aşağıda uygulamadan buna örnek bir sınıf görüyorsunuz: 

***Bean.java sınıfı***

![](http://i.hizliresim.com/RYJag7.png)

Nesne için oluşturduğumuz ***yapıcı*** (constructor) metot, id, english, turkish, status değerlerini hızlı bir şekilde atamamızı sağlıyor. Buradaki sınıfa göre kaynak kod içinde `new Bean( id,english,turkish,status)` şeklinde bir deyimle nesne oluşturursak, bu kelime bilgisine sahip bir kelimeyi temsil eden bir ***Bean*** nesnesi oluşturmuş olacağız.

***Listede gösterilecek verileri oluşturmak***

Veri modelimize uygun bir sınıf oluşturduğumuza göre listemizde görüntülenecek veri bütününü-listeyi de oluşturabiliriz. Bunun için içini Bean nesneleriyle dolduracağımız bir ArrayList yeterli gelecektir. Hemen Dictionary dosyamızı açalım ve bir ArrayList oluşturalım:

![](http://i.hizliresim.com/vEg0NA.png)

***wordList*** isimli ArrayList'imizi Dictionary sınıfının bir alanı (field) şeklinde tanımladık. 

***Custom bir Adapter oluşturmak***

ListView'ı verilerle doldurabilmek için bir Adapter oluşturmamız gerekir. Kendi Adapter nesnemizi oluşturabilmek için Android SDK'de yer alan BaseAdapter sınıfını temel sınıf olarak kullanabiliriz. Bu sınıftan türetilen sınıfların sahip olması/override etmesi gereken dört metot vardır:

•	***getCount()***: int değer döner. ListView'da gösterilecek satır sayısını ifade eder. Verilerimizi barındıran ArrayList'in boyutu (size()) burada bize yarayacak.

•	***getItem(int position)***: Object değer döner. position ile belirtilen satıra denk düşen nesneyi döndürür. Bu nesne satır olarak gösterilecek nesnedir. Bundan dolayı Object yerine doğrudan model sınıfınızdan oluştuğunuz nesneyi de dönüş türü olarak belirleyebilirsiniz.

•	***getItemId(int position)***: long değer döndürmelidir. Veri listesinde position ile sırası belirtilen satırın kimlik numarasını (id) döndürür. Liste içeriğini veri tabanına kaydedecekseniz ya da orada eşlemeler yapacaksanız önem kazanır.

•	***getView(int position, View convertView, ViewGroup parent)***: View değer döner. position ile sırası belirtilen satır için bir View döndürür. Bu metot içindeyken her satır için XML'i okuyup View haline getirme işlemi (inflating) yaparız. Bu hususta bize LayoutInflater servisi yardımcı olacaktır.
İncelemeye yapılandırıcı metottan başlayalım:

![](http://i.hizliresim.com/XvBn85.png)

Yapılandırıcı metodumuzun ilk parametresi, Adapter'ın bağlı bulunacağı uygulama parçasıyla (ListView) ilgili: ***Context*** yani Bağlam. Böylece `WordListAdapter`'a çalışacağı ortamla ilgili temel bilgileri bu parametreyle geçirmiş oluyoruz.

`List<Bean> wordList` parametresi de adaptörümüzün ihtiyaç duyacağı verileri alacağımız yer. `<Bean>` ifadesiyle içinde sadece Bean bulunduran List'i kabul ettiğimizi belirtiyoruz. Verileri alma işlemini `wordList = new ArrayList<Bean>` ifadesiyle hallediyoruz.

`mLayoutInflater` değişkenine activity üzerinden bir sistem servisini referans gösteriyoruz: Layout Inflater Service. Bu servis, yerleşimleri kullanıcıya gösterebilmek için onları önce XML'den okuyup ardından View'a çevirme işlemini yapar.

`getCount()` ve `getItemId()` metotları, yukarıda tanımladığımız işlemleri yapıyor. `getItem()` metodu da aynı şekilde wordList'in position indisine sahip nesneyi döndürüyor.

Önemli bir metot olan getView()'a göz atalım:

Bu metodun bookmark ile olarak gösterilecek View'ı döndürdüğüne yukarıda değinmiştik. Bu amaçla view adında bir View oluşturuyoruz. Sonra bu View'a
`word.xml` dosyasındaki yerleşimi veriyoruz. Böylece view üzerinden
word.xml'deki öğelere erişebiliyoruz:


![](http://i.hizliresim.com/l7koRg.png)

Dikkat ederseniz `findViewById()` metodunu view üzerinde çalıştıyoruz.
Ardından View olarak döndürülecek satır için Kisi nesnesini yine position indisiyle wordList üzerinden alıyoruz ve word değişkenine atıyoruz.
word referansı üzerinden Bean nesnesinin  id, status, english, turkish gibi değişkenlere erişilebilir.

Tüm bu işlemlerin ardından view hazır oluyor ve onu return ediyoruz.

***2. Bookmarked.java***

![](http://i.hizliresim.com/ZDLrBg.png)

***3. TurkishDataLoader.java***

![](http://i.hizliresim.com/Nq5y0Y.png)

•	***doInBackground(Params... params)*** : onPreExecute() metodundan hemen sonra çalışır. Arkaplan işleminin yapıldığı kısımdır.Bu fonksiyon AsyncTask’ın  en temel fonksiyonudur.Bu fonksiyon tanımlanmaz ise AsyncTask’ın hata verdiğini görürsünüz çünkü AsyncTask temel olarak bu fonksiyon için oluşturulmuş bir yapıdır. Bu metod içine uzun süreceğini düşündüğümüz işlemleri kodlarız.UI yada Main threadını kilitleyecek programın terminate edilmesine neden olacak işlemlerin yapıldığı kısımdır. doInBackground  fonksiyonu params ile gösterilen şekilde arka plan işlemlerinde kullanılmak üzere multiple parametre alabilir. Arkaplan işlemi bitince `onPostExecute()` metodu çağrılır.

•	***onPostExecute(Result result)*** : doInBackground() da yapılan işlem biter bitmez çağrılır. Arkaplan işleminin döndüğü değerleri handle ettiğimiz yani  elde edilen verileri kullanabileceğimiz yerdir. ProgressDialog  gibi bilgilendirme ekranları, bu metod içinde dismiss edilir.

***4. menu.xml***

![](http://i.hizliresim.com/d52ONV.png)

***5. word.xml***

![](http://i.hizliresim.com/qY4gZV.png)

***6. notification_add.xml***

![](http://i.hizliresim.com/94V6BO.png)

***7.bookmarked.xml***

![](http://i.hizliresim.com/qYlGp5.png)

NOT: Örnek uygulamanın kaynak kodları [githubtadır.](https://github.com/elifbesli/Android/tree/master/Turkish%20Dictionary)



***PROJE HAKKINDA***

----------


![](http://i.hizliresim.com/oggPa9.png)

Okul : Ondokuzmayıs Üniversitesi , SAMSUN

Fakülte: Mühendislik Fakültesi

Bölüm: Bilgisayar Mühendisliği Bölümü

Proje Adı : Mobil Programlama Dersi Vize Projesi

Ders Danışmanı : Öğr.Gör.Dr. İsmail İŞERİ

----------


Hazırlayan : Elif BEŞLİ - Bilgisayar Mühendisliği /3.Sınıf

e-posta adresi : elif.besli@bil.omu.edu.tr

Tarih: 28 / 04 / 2015







