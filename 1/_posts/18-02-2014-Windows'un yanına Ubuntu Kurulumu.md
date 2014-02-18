---
layout: post
title: Windows'un yanına Ubuntu kurmak
---

Bu belgede, Windows yüklü bir bilgisayarda Windows kaldırılmadan, sabit diskin bir başka bölümüne Ubuntu'nun kurulması konusu anlatılmaktadır. Bu şekilde iki işletim sistemi ayrı ayrı çalışırılarak sorunsuzca kullanılabilir.
##1. Yöntem##
Kendinize bir Ubuntu DVD'si ya da Ubuntu USB belleği hazırladıysanız artık Ubuntu kurulumuna geçebilirsiniz.

Ubuntu'yu bilgisayarınıza kurmak için Ubuntu'ya ayırabileceğiniz ayrı bir disk bölümünüzün olması gereklidir. Yani nasıl ki Windows işletim sistemi genellikle “Yerel Disk (C:)” isimli disk bölümü içinde yer alıyorsa Ubuntu için de bunun gibi ayrı bir disk bölümüne ihtiyaç vardır. Halihazırda Ubuntu için kullanabileceğiniz ayrı bir bölümünüz olmasa bile anlatacağımız bu 1. kurulum yönteminde, Ubuntu için disk bölümü ayırma işlemi otomatik olarak yapılacaktır. Bu yöntem, deneyimsiz kullanıcıların Windows'u kaldırmadan kolayca Ubuntu kurmasını sağlar. Bununla birlikte bu yöntemde, kullanıcı Ubuntu'yu hangi disk bölümü içine kuracağını tam olarak belirleyemediği için ve disk bölümlendirme işlemini kendisi yapamayacağı için bu yöntem bazı kullanıcılar için yetersiz gelecektir.
1. Yöntemde işlemlere başlamak için Ubuntu kurulum adımlarını gerçekleştirmeye başlayın. Alttaki resimdeki görüldüğü gibi kurulum türü belirleme ekranına geldindiğinizde en üstteki seçenek olan "Ubuntu dağıtımını Windows işletim sisteminin yanına kur" seçeneğini işaretleyerek devam edin.

![alt text](http://wiki.ubuntu-tr.net/images/3/37/Kurulum_03-1.png"Title")

Devam ettiğinizde, Ubuntu sabit diskimizdeki tüm bölümleri tarayacak ve içinde yeterli boş olan bulunan bir disk bölümünü tespit ettiğinde o disk bölümünün içindeki verilere ya da işletim sistemi dosyalarına zarara vermeden ikiye bölmek üzere alttaki gibi bize sunacaktır.

![alt text](http://wiki.ubuntu-tr.net/images/f/f6/Otomatik_disk_b%C3%B6l%C3%BCm%C3%BC_ay%C4%B1rma.png"Title")

Burada ikiye bölünecek disk bölümünde parçaların hangi boyutlarda olacağını belirlemek için fare imleci ile iki bölüm arasındaki ayracı tutup sağa ya da sola doğru hareket ettirebilirsiniz.
"Şimdi Yükle" düğmesine tıklayarak Ubuntu kurulumunu gerçekleştirebilirsiniz.

##2. Yöntem##
Eğer Ubuntu'yu kendi belirlediğiniz disk bölümüne kurmak istiyorsanız ya da diskinizi kendi ihtiyaçlarınız doğrultusunda bölümlendirmek istyorsanız bu yöntemi kullanabilirsiniz.
##Diskin elle bölümlendirilmesi##
Sabit diskinizde Ubuntu için bir yer bakarken bir de belgelerimiz için ayrı bir yer (disk bölümü) ayırmamız da büyük fayda vardır. Belgelerinizi Windows'un ya da Ubuntu'nun kurulu olduğu bölümler dışında sadece belge depolama amaçlı bir bölümde tutmanız verilerinizin güvenliği açısından önemlidir. Böylece, herhangi bir sebepten dolayı Windows ya da Ubuntu'nun çökmesi halinde verilerinize zarar vermeden bu işletim sistemlerini yeniden yükleyebilirsiniz. Bu nedenlerden dolayı sabit diskinizi en az dört parçalı olacak şekilde yapılandırmanızı tavsiye ederiz.


![alt text](http://wiki.ubuntu-tr.net/images/a/a5/%C3%96rnek_disk_yap%C4%B1s%C4%B1.png"Title")

###Belgelik bölümü:###
 Bilgisayarınızda hangi işletim sistemlerini kullanırsanız kullanın, belgelerinizi (resim, müzik, belge, vb.) işletim sistemlerinin kurulu olduğu disk bölümleri dışında ayrı bir bölüm içinde kullanmanız daima yararınıza olacaktır. Bu yüzden bilgisayarınızda halihazırda belgeleriniz, Windows'un kurulu olduğu (C:) disk bölümü içerisinde ise ayrı bir bölüm açarak belgelerinizi daima orada bulundurmanızı tavsiye ederiz. Belgelerinizi bulunduracağınız bu disk bölümüne hem Windows'da çalışırken hem de Ubuntu'da çalışırken ulaşabilmeniz için bu bu disk bölümünün NTFS dosya sistemi ile biçimlendirilmiş olması gereklidir.
Ubuntu‘yu kuracağımız bölüm: Bu bölüm minimum 5 GB boyutunda olabilir ancak Ubuntu'ya sonrada kurabileceğiniz programları oyunlar vb. göz önüne alarak en az 15 - 20 GB lık bir yer ayırmanızda fayda vardır. Bu bölüm Ubuntu kurulumu sırasında ext4 dosya sistemi ile biçimlendirilecektir, önceden biçimlendirmenizin bir gereği yoktur.
###Takas alanı (swap):###
 Bu bölüm Ubuntu'da çalışırken bilgisayar belleğinin yetersiz kaldığı durumlarda Ubuntu'nun bu disk bölümüne başvurarak açığı kapatmasını amaçlayan bir bölümdür. Takas alanı için 2 ila 4 GB civarında bir alan ayırmanız tavsiye edilebilir. Takas alanı için çok büyük bir alan ayırmanızın performans açısından ek bir faydası olmayacaktır. Ubuntu kurulumu sırasında takas alanı olarak belirlediğiniz bölüm, otomatikman "swap" dosya sistemi ile biçimlendirilecektir, yani önceden biçimlendirmenizin bir gereği yoktur.
####Peki sabit diskimizi nasıl bu şekilde bölümlere ayıracağız?####
Aslında Ubuntu'nun kurulumu esnasında, Ubuntu'nun kendi gelişmiş disk bölümlendirme özelliği kullanılarak disk bölümlendirme işlemleri gerçekleştirilebilir, ancak disk bölümlendirme gibi kritik bir işi kurulumu öncesinde tamamlayıp Ubuntu'yu daha rahat bir şekilde kurmanızı tavsiye ederiz.
Sabit diskinizi ihtiyaçlarınız doğrultusunda bölümlere ayırmak için Windows'un Vista ve sonraki sürümlerde yer alan, Windows'un kendi disk bölümlendirme aracını kullanabilirsiniz ya da GParted gibi Çalışan CD/USB destekli bir disk bölümlendirme aracını kullanabilirsiniz.

Windows Vista ve sonraki sürümlerde yer alan, Windows’un kendi disk yönetim aracını kullanmak için Bilgisayar simgesine sağ tıklayıp Yönet yolunu izleyerek ya da Başlat menüsündeki arama kutucuğuna bilgisayar yönetimi yazarak Bilgisayar Yönetimi penceresini açın. Pencere içinde sol taraftan Disk Yönetimi seçeneğine tıklayın.

![alt text](http://wiki.ubuntu-tr.net/images/d/d3/Bilgisayar_-_ters_t%C4%B1k.png"Title")
![alt text](http://wiki.ubuntu-tr.net/images/1/1b/Windows-Disk_Y%C3%B6netimi_01.png"Title")

Yeni bir bölüm oluşturmak için Disk Yönetimi arayüzünde mevcut disk bölümlerinden birini (“C” bölümü de dahil olmak üzere), içindeki boş alan kadar küçültmek suretiyle yeni bir disk bölümü oluşturabilirsiniz. İçinde belli bir miktar boş alan bulunduran bir disk bölümünü küçülterek ikiye ayırmak için o disk bölümüne sağ tıklayın ve açılan menüdeki "Birimi Küçült" seçeneğini kullanın. 
##Ubuntu’nun kurulması##
Ubuntu kurulumunda disk bölümlendirme ile ilgili olarak, ilk önce alttaki görüntüyle karşılaşırsınız.

![alt text](http://wiki.ubuntu-tr.net/images/3/37/Kurulum_03-1.png"Title")

Burada "Başka bir şey" seçeneğini kullanarak devam etmelisiniz. Ardından alttaki gibi tüm disk bölümlerinizin listelendiği gelişmiş disk bölümleri tablosu karşınıza gelir.
Disk bölümleri tablosunda Windows’un C, D, ... disk bölümleri C , D harfleri ile karşınıza gelmeyecektir! Burada tüm disk bölümlerinin boyutları, dosya sistemleri hakkında bilgiler verilecektir, siz bu bilgilere bakarak hangisinin C hangisinin D olduğunu anlayabilirsiniz.

![alt text](http://wiki.ubuntu-tr.net/images/0/06/Geli%C5%9Fmi%C5%9F_disk_b%C3%B6l%C3%BCmlendirme_01.png"Title")

Listeden, Ubuntu kurmayı planladığınız bölüme çift tıklayınız. Karşınıza aşağıdaki gibi bir pencere gelecektir.Burada "Boyut" kısmına söz konusu disk bölümünün tüm boyutunu girin.

![alt text](http://wiki.ubuntu-tr.net/images/2/2e/Geli%C5%9Fmi%C5%9F_disk_b%C3%B6l%C3%BCmlendirme_02.png"Title")

"Nasıl kullanılacağı" kısmında Ext4 seçeneğini işaretleyin.

"Bağlama noktası" olarak da "/" işaretini seçin (bu işaret, kök dizin anlamındadır ve Linux dosya sistemi hiyerarşisi ile ilgilidir...) ve Okey tuşuna basarak pencereyi kapatın.

Ardından disk bölümleri tablosunda Takas alanı olarak kullanmayı düşündüünüz alana çift tıklayın. Karşınıza yine yandaki gibi "Bölüm Düzenle" penceresi gelecektir. Bu sefer "Nasıl kullanılacağı" kısmında "takas alanı" seçeneğini işaretleyin ve Okey tuşuna basarak pencereyi kapatın.

Disk yapılandırmamızı tamamladık. Şimdi disk bölümler tablosunda, Ubuntu'yu kurmak istediğimiz alana bir kez tıklayıp ardından Şimdi Yükle düğmesine tıklayarak kuruluma devam edebilirsiniz. Devamında yerel saat seçimi, klavye düzeni seçimi gibi aşamaları geçerek kurulumu tamamlayabilirsiniz.
