---
layout: post
title: CameraApp - Android Kamera Uygulaması
---


Android uygulamalar üzerinden kamera bağlantısı iki türlü yapılabilmektedir. İlk yöntem uygulamadan fotoğraf çekme isteği geldiği zaman, Android üzerinde var olan kamera uygulamasının   başlatılması   ve   fotoğraf   çekildikten   sonra veriyi   uygulamaya   çekmek şeklindedir. İkinci yöntemde ise kameradan alınan görüntü direkt uygulama içerisine entegre edilip fotoğraf çekilmesi işlemini uygulamadan kontrol etmektir.

Bu projede ikinci yöntem tercih edilmiştir. Kamera görüntüsünü cihaza entegre edip fotoğraf çekmek için öncelikle yeni bir aktivite oluşturuldu. Bu aktivitenin layout (aktivitenin görünüş düzeni)’ı içine kameradan gelen görüntünün gösterileceği bir FrameLayout konulmuştur.


###ÖN HAZIRLIK

----------

Bu  kısımda  öncelikle  donanıma  ulaşabilmek  için  izinleri  aktif  hale  getirmemiz  gerekiyor. Bunun  için  AndroidManifest.xml  dosyasını  açıyoruz  ve  **<uses-sdk/>**’nın  bittiği  satırdan itibaren şu izinleri belirtiyoruz :



<img src =" http://i.hizliresim.com/mrRN2R.png">

İzinleri hallettikten sonra, aktivitemizin main.xml adlı layout (aktivitenin görünüş düzeni)’ı içine kameradan gelen görüntünün gösterileceği ve Surfaceview görselini barındıran bir FrameLayout konulmuştur.

<img src =" http://i.hizliresim.com/5oqnnz.png">


###1. Kamera Önizlemesinin Oluşturulması
---
Bunun için CameraActivity adında bir aktivite sınıfı ve bu aktivitenin kamera görüntüsü elde edebilmesi için kullandığı Preview adında bir sınıf yaratıldı.

###1.1. Preview Sınıfı

----------

Kamera  canlı  görüntüsünün  tutulabilmesi  için Android’in  şart koştuğu kurallar vardır. Bu kurallardan bir tanesi SurfaceView sınıfını extend edip SurfaceHolder.Callback interface’ini de implement eden bir sınıf olmasıdır. Projedeki Preview sınıfı bu şartı sağlayan sınıftır.

*public class Preview extends SurfaceView implements*


*SurfaceHolder.Callback { …*


*Bu sınıfın override etmesi gereken üç metod bulunmaktadır; surfaceCreated(), surfaceChanged()*

*ve surfaceDestroyed().*


***SurfaceView***,  görüntüleme sistemi için geliştirilmiş bir çizim aracıdır. Kamera önizlemesi, oyunların ekrana çizdirilmesi, paint uygulamaları gibi işlemler için kullanılan bir çizim alanıdır. Boş bir resim kağıdı veya defter gibi de düşünülebilir.

***SurfaceHolder***,   oluşturulan   görüntüyü   ekranda   sürekli   tutmak   için   kullanılır.   Ayrıca

SurfaceView’in boyut, format gibi bilgilerini de kontrol eder.


***SurfaceHolder.CallBack***,  Surfaceview’deki  değişikliklerde  bilgi  almak  için  kullanılan  bir

arabirimdir.


***SurfaceChanged***, SurfaceView herhangi bir değişikliğe uğradığında çalışacak ilk metottur.


***SurfaceCreated***, SurfaceView başladığında çalışa metottur.


***SurfaceDestroyed***, SurfaceView kapatıldığında çalışacak olan metottur. 


<img src =" http://i.hizliresim.com/pm2NrL.jpg">
<img src =" http://i.hizliresim.com/g4bv9L.jpg">


 

###1.2. CameraActivity Sınıfı

----------

 Bu sınıfımızda onCreate(), onResume(), onPause() gibi Android Lifecycle metotlarından bazıları tanımlanmıştır.

###1.2.1. onCreate Metodu 

----------

 Aktivitemizin OnCreate metotu içinde aktivite başlatıldığında yapılacak olan işlemler tanımlandı. Preview sınıfının kullanılması için, öncelikle preview tipinde iki parametreli bir nesne oluşturuldu.

Ekranı layout   dosyası kullanılmadan programlamak için LayoutParams  kullanıldı. LinearLayout, RelativeLayout vb. gibi ViewGrouplar, LayoutParams içerik sınıfı içerir. LayoutParams oluşturulacak layoutun özelliklerini  içerir.  Preview  nesnesini  layoutta  hangi yükseklik  ve genişlikte  durması gerektiğini  aşağıdaki kod yardımıyla belirliyoruz  ve addView ile özellikleri artık belirlenmiş olan preview nesnesini Layout’ımıza ekliyoruz :

<img src =" http://i.hizliresim.com/g4bvkO.png">
setContentView metodu ile UI ekranı kullanıcıya gösterilerek aktivite başlatılmış olunur. Activity  sınıfından  türetmiş  olduğumuz  sınıflar  bir  üst  sınıfın  yani  activity  sınıfının onCreate metodunu çağırmalıdırlar. Aksi halde istisna oluşmaktadır. Bu işlemlerden sonra setContentView(R.layout.main); metodu  ile  idenin  oluşturmuş  olduğu,  ilk  görüntüdeki ekranı kullanıcıya göstermiş oluyoruz.


####1.2.2. onResume Metodu

----------

Activity  herhangi  bir  sebepten  dolayı  durdurulduysa  (başka  bir  ekrana  geçiş)  tekrar ekrana geldiğinde bu metot harekete geçer. Activity çalıştırılmadan önce harekete geçen son metottur. Bu metottan sonra belirlenen koda göre Activity yaşamına başlar. Örneğin ilerleyen bölümlerde yer alacak olan ImageViewin yer aldığı layout dosyasından yani alınan görüntünün ekrana getirilmesinden sonra tekrar main aktivitesine geçiş esnasında bu metot çalışır.

numCams  değişkeni ile  telefonda  kamera  olup olmadığının ya  da  varsa  kaç kamera olduğunun tespiti yapılır. Open(0) ile ön kameranın kullanılması sağlanır. Kameradan alınan görüntüler preview nesnesine atanır . 


<img src ="http://i.hizliresim.com/rmyvX1.png">

####1.2.3. onPause Metodu

----------

Activity bir sebepten dolayı arka plana atılırsa harekete geçer. Bu **Back** tuşuna basılmasından ya da sistemin bellek ihtiyacından kaynaklanabilir. **onPause** işlemi hızlı çalışmak zorundadır, çünkü  buradaki  işlem  bitmeden  bir  sonraki Activity harekete  geçmez.  Bu  metotta  Back’e basıldığında kamera önizlemesinin durdurulması sağlandı.


<img src ="http://i.hizliresim.com/89XWZA.gif">

<img src ="http://i.hizliresim.com/GpGWm7.jpg">


<img src ="http://i.hizliresim.com/LYG8vG.jpg">

###2. Fotoğraf Çekilmesi

----------


Kamera önizlemesinin ardından fotoğraf çekme işleminin gerçekleştirilmesi için gereken adımlar :

**1**. Kamera üzerinde yer alacak butonları kontrol etmek amaçlı control.xml dosyası oluşturuldu. Bu layout dosyasına aktivitemizden erişmek için LayoutInflater sınıfı kullanıldı.

Layout Inflater, bir XML kaynak dosyasını (layout barındırmasa da olur, hazır veya kendi oluşturduğunuz/özelleştirdiğiniz  bir  View  nesnesine  de  ait  olabilir)  alıp,  onu  bir  View nesnesine çevirmeye ve üzerinde bu yolla işlem yapmaya imkan sağlayan bir sınıftır. Haliyle View objeleri üzerinde alışılmışın üstünde işlemler yapıldığında (Activity'nin onCreate(), onResume() gibi zamanlarında değil de, daha çok kullanıcıyla etkileşim sırasında, değişen bir şarta   göre   -çalışma   zamanında-   yapılacak   değişiklikler),onları   bir   ViewGroup   altında kullanacak  olunduğunda  vs.  kullanılıyor.  Bir  view  içine  başka  bir  view koyarak  ona  şekil verebilirsiniz.

**2**. **control.xml** dosyasına fotoğraf çekme butonu eklendi. 

<img src ="http://i.hizliresim.com/J7W88Y.jpg">


**3**. Fotoğraf çekme işleminin gerçekleşmesi Camera’nın camera.takePicture(**shutterCallback, rawCallback, jpegCallback**) metodu çağrıldı. Fotoğraf çekilme tuşuna basıldığında kendi PictureCallback sınıfının onPictureTaken() metoduna resim gönderilir. Bu metot override edilerek kayıt boyutunun küçük olması için fotoğrafın kalitesi düşürülüp hafıza kaydına kaydedilir.

<img src ="http://i.hizliresim.com/O3a8OD.jpg">


takePicture butonuna basıldığında ShutterCallback ve PictureCallback adlı sınıfların metotları çağrılır.

**ShutterCallback** metodu ile denklanşöre basıldığı an dinlenir. Bu metotta genellikle denklanşör sesi ve o an için ekrana koyulacak herhangi bir görsel veya animasyon yerleştirilir. Fotoğrafın çekildiği anda ekran donar, bu da istenmedik bir görüntüye neden olur. Bunun yerine küçük bir animasyon uygulamayı daha kullanışlı hale getirecektir.

**PictureCallback rawCallback**  ile fotoğrafın hiç işlenmemiş haline ulaşılabilir. Fotoğrafa herhangi bir müdahalede bulunmak isterseniz en ham veri burada bulunur.

**PictureCallback  jpegCallback**  ile    yaygın  olarak  kullanılan  jpeg  tipindeki  datalar  bize ulaştırılır. Buradaki veriler byte tipindedir.


<img src ="http://i.hizliresim.com/7VQWnL.jpg">


<img src ="http://i.hizliresim.com/ZAnjo3.jpg">

###3. Çekilen Fotoğrafın Ekranda Gösterilmesi

----------

Çekilen fotoğrafın ekranda görüntülenmesi için control.xml dosyasına bir ImageView eklemesi yapıldı. ImageView, uygulamalarda resim görüntülemek amacıyla kullanılan bir bileşendir. Yani çekilen fotoğraf ImageView aracılığı ile görüntülenir.

<img src ="http://i.hizliresim.com/VgB8jj.jpg">

ImageView’e çekilen fotoğrafların alınabilmesi için fotoğrafların byte tipinde değil Bitmap tipinde olması gerekir. Bu nedenle onPictureTaken() metoduna gelen byte tipindeki datalar bitmap’e çevrildi. Daha sonra setImageBitmap(Bitmap) metodu ile bitmaplar imageview’e aktarıldı. Aynı anda, kameranın arka planda çalışmasının durdurulması için Visibility durumu **GONE** yapıldı. 

<img src ="http://i.hizliresim.com/ZAnj4g.jpg">

<img src ="http://i.hizliresim.com/oYyqD7.jpg">

###4. Çekilen Fotoğrafa Filtre Uygulanması

----------



Control.xml dosyasına efekt butonu eklendi. Filtreleme işlemlerini yapan ImageFilters ve ConvolutionMatrix adlı iki sınıf oluşturuldu. Burada örnek olarak sadece bir efekt üzerinden açıklanmaya çalışılacaktır. Aşagıdaki örnekte sadece gri efekti vermesi için bir buton oluşturuldu. 

<img src ="http://i.hizliresim.com/YjV8YD.jpg">

----------
Gri efekti üzerinden gitmek gerekirse, control.xml dosyasında oluşturulan button3 id’sine sahip buton için greyButton adında bir buton nesnesi oluşturuldu. Bu butona tıklandığında yapılacak işlemler sırasıyla şöyle olacaktır: Öncelikle bu butona basılma sayısını tutan int tipinde sayaç görevi yapacak olan bir i değişkeni tutuldu. Butona ilk kez tıklanıldığında ilgili filtre uygulanır, ikinci tıklamada ise filtre yok olur. Tıklama sayısının çift olup olmadığının anlaşılması için mod işlemi kullanıldı. Filtre uygulama işlemi butona ilk kez tıklandığında yani i%2==1 iken gerçekleşeceğinden bu kısımdan başlayalım. Filtreleme işleminin gerçekleşebilmesi için ImageFilters sınıfının applyGreyscaleEffect() metodu kullanılmalıdır. Bu metodu kendi sınıfımız içerisinde kullanabilmemiz için, ImageFilters sınıfından bir nesne oluşturuldu. applyGreyscaleEffect(bmp) metodundaki bmp adlı Bitmap değişkeni onPicturetaken metodundaki dataların Bitmap’ dönüşmüş şeklidir. Daha sonra tekrar filtresiz
hale dönebilmek için, filtre uygulanmış Bitmap değişkenini m adlı Bitmap tipinde ayrı bir değişkende tutuldu. Yani m Bitmap şeklindeki datanın filtreli halidir. Ve son olarak da Bitmapteki değişikliklerin ekranda görüntülenmesi için m adlı Bitmap değişkeni ImageView tipindeki iv değişkenine aktarıldı.

<img src ="http://i.hizliresim.com/4Z9OQJ.png">



<img src ="http://i.hizliresim.com/BRGN99.jpg">

###5. Fotoğrafın Kaydedilmesi

----------



Bu işlemin gerçekleşmesi için, control.xml dosyasına eklenmesi gereken kod bölümü aşağıdaki gibidir :

<img src ="http://i.hizliresim.com/D8GmMO.jpg">

**AsyncTask**, android uygulamalarında uzun sürecek işlemlerin arka planda yapılmasının sebebi ana akışın (Main Thread ya da UI Thread) uzun süre engellenmemesidir. Eğer ana akışı uzun süre bloke ederseniz uygulamanızın ana ekranı donacak ve kullanıcı uygulamanızın bozulduğunu düşünecektir. AsyncTask ile kaydetme işlemi arka planda ana akışa etkisi olmayacak  şekilde  gerçekleşir.  Bu  işlem  arka  planda  devam  ederken  uygulamada  başka işlemler yapılabilmesine de olanak sağlar.

<img src ="http://i.hizliresim.com/D8GmRv.jpg">

Bu sınıf oluşturulduktan sonra yapılması gereken dataların elde edildiği yer olan onPicturetaken() metodunda bu sınıftan nesne oluşturup, byte tipine execute etmektir. Bu işlemden sonra fotoğraf çekildiği anda galeride yarattığımız klasöre kaydolur.

<img src ="http://i.hizliresim.com/bg9pJZ.jpg">

Fotoğrafın filtreleme işlemi sonucunda farklı kaydedilmesi için:

<img src ="http://i.hizliresim.com/vQyV5A.jpg">

**SaveImageTask** sınıfı byte tipinde data kabul ettiği için execute metodundan faydalanılır. Fakat farklı kaydet butonu araya girdiğinden, datayı fotoğrafın çekildiği anda değil de kaydet butonuna basıldığı anda kaydedebilmek için –yani filtreli hali ile- aşağıdaki yol izlenmelidir. Bu yöntem ile local değişken olan data, her yerden ulaşılmak için byte tipindeki c değişkenine atandı. Yani filtreli Bitmap olan m’nin, saveFile butonuna tıklanıldığında onClick metodu içerisinde execute edilebilmesi için tekrar byte tipine dönüştürüldü.
 
<img src ="http://i.hizliresim.com/89X5PW.jpg">
###6. Fotoğrafın Paylaşılması

----------



control.xml dosyasına bu işlemi gerçekleştirecek bir share butonu eklendi.

<img src ="http://i.hizliresim.com/g4bpqN.jpg">

Share butonu altında değinebileceğimiz en önemli nokta byte tipinde olan verilerin tekrar bitmap’a dönüştürülmesidir.

<img src ="http://i.hizliresim.com/rmyp5z.png">

<img src ="http://i.hizliresim.com/127Bpb.jpg">

###Uygulamanın Son Halinden Görüntüler

----------


 **NOT:** ImageFilters ve ConvolutionMatrix sınıflarına [linkten](https://github.com/elifbesli/CameraApp) ulaşabilirsiniz.



<img src ="http://i.hizliresim.com/D8GmgO.jpg">

<img src ="http://i.hizliresim.com/vQyVWv.jpg">

<img src ="http://i.hizliresim.com/qmypZR.jpg">

<img src ="http://i.hizliresim.com/D8Gm0v.jpg">

<img src ="http://i.hizliresim.com/04rg5B.jpg">

<img src ="http://i.hizliresim.com/jvAp7m.jpg">

<img src ="http://i.hizliresim.com/127BRN.jpg">

<img src ="http://i.hizliresim.com/6lMbj0.jpg">


