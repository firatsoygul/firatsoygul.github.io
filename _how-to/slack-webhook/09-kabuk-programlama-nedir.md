---
title: "Kabuk Betiği Oluşturma"
permalink: /how-to/slack-webhook/kabuk-programlama-nedir/
excerpt: "Kabuk programlama nedir, nasıl yapılır."
last_modified_at: 2018-06-16T21:20:00-10:00
classes: wide
tags:
  - Slack
  - Linux
  - Kabuk Programlama
  - Kabuk Betiği
  - Bash
---

Şu ana kadar oluşturduğumuz kodları, komut satırı üzerinde çalıştırdık. Yapmak istediğimiz her işlemde, bu uzun komutu tekrar komut satırına girmemiz gerekiyordu. Daha doğrusu kodlarımızı, tek seferde / satırda çalıştırılabilir şekilde dizayn ettik. Bu şekilde oluşturulan kodların tekrar kullanımları sırasında yeniden yazılması, içerdiği değerlerin yeniden düzenlenmesi gerekiyordu. Her ne kadar terminal hafızasını kullanarak son kullanılan komutları yeniden çağırabiliyor olsak da, kod bloğumuz uzadıkça iç içe geçen kod yapıları oluşturmak zorunda kalıyoruz ve bu durum da kodumuzun okunabilirlik düzeyini oldukça düşürüyor.

Ayrıca gömülü Linux sistemi barındıran bir IOT donanımında geliştirdiğimiz uygulamalardan mesaj gönderebilmek için, bu kodu geliştirdiğimiz her uygulamaya dahil etmemiz gerekecekti. Ancak bu işlem taşınabilirlik ve güncelleme gibi konularda problemler yaratacağından sağlıklı bir yöntem olmayacaktır.

Böylesi durumlarda kabuk programlama yöntemi kullanılıyor. Bu yöntemde, çok daha karmaşık komutları bir araya getirerek bir dosyaya yazıyoruz (bu toplu işlem dosyalarına, kabuk programı / betiği (shell script) deniliyor). Ve dosyayı çalıştırıyoruz. Daha doğrusu kabuğun (shell) bu dosya içindeki komutları tek tek çalıştırmasını sağlıyoruz.

Bu programları diskimizde barındırabilir, açılışta çalışmasını sağlayabilir veya herhangi bir IoT donanımının kontrolüne sunabiliriz.

**Uyarı:** Linux sistemlerinde çalışan bir çok kabuk programı var. Bunlar hakkında bilgi için [bu makaleyi](http://yunus.hacettepe.edu.tr/~yurdugul/3/indir/KabukProgramlama.pdf) inceleyebilirsiniz. Biz yazacağımız kodları [Bash](https://www.gnu.org/software/bash/) kabuğunda çalıştıracağız. Sisteminizde Bash kabuğunun yüklü olup olmadığını öğrenmek için (birçok Linux dağıtımında standart olarak geliyor) `cat /etc/shells` komutunu kullanabilirsiniz. Komutu girdiğinizde, sisteminizde mevcut tüm kabuklar listelenecktir. Liste içinde Bash kabuğuna ait `/bin/bash` dizin yolunu görüyorsanız sisteminizde yüklü demektir. Eğer yüklü değilse [burada](https://www.gnu.org/software/bash/manual/html_node/Installing-Bash.html) bulunan talimatları izleyerek yüklemeniz gerekiyor.
{: .notice--warning}

Öncelikle çalışma dizinimizde `slackCLI.sh` adında bir dosya oluşturalım.

**Bilgi:** Dosya ismini dilediğiniz gibi oluşturabilirsiniz. Ayrıca, Linux dosya uzantılarını dikkate almadığı için dosya uzantısını oluşturmanıza da aslında gerek yok. Ancak dosya hakkında bize bilgi vermesi açısından, Bash betik dosyalarının uzantısı olan `.sh` uzantısını verebilirsiniz.
{: .notice}

Sisteminizde farklı bir kaç kabuk yüklü olabilir. Ayrıca farklı sistemler öntanımlı olarak farklı kabuklar kullanıyor da olabilir. Ön tanımlı kabuk, Linux işletim sisteminizin ilk açılışında ön tanımlı olarak başlayan ve kullanımınıza sunulan kabuktur. Bizim geliştirdiğimiz bu kabuk programı her ne kadar Bash komutları kullanarak yazılıyor olsa da, çalıştırmak için ön tanımlı kabuğunuzu değiştirmenizin bir gereği bulunmuyor. Oluşturacağımız kabuk programlarının öntanımlı kabuk ile değil de, her defasında bizim belirttiğimiz kabuk (Bash) ile çalıştırılması için, dosyayı herhangi bir metin editörü ile açarak ilk satırına aşağıdaki kodu ekliyoruz.

```bash
#! /bin/bash
```

Artık kabuk komutlarımızı bu kodun altına yazabiliriz.

Uygulama esnasında hiç değişmeyecek olan bazı sabit verilerimiz var. Mesela Webhook Url. Bu bağlantıyı her defasında girmek yerine dosyamızın içine bir değişken olarak tanımlayabiliriz. Ayrıca göndericinin bazı bilgileri (kanal, gönderen adı, icon vb.) boş bırakma ihtimaline karşı, bu verilere ait bir ön tanımlı değer oluşturabiliriz. Daha önce Webhook Url oluştururken, **Edit Configuration** sayfasında birer ön tanımlı değer belirlemiştik. Şimdi belirleyeceğimiz öntanımlı değerler ise uygulamamız bazında ele alınacak. Şimdi her değer için bir değişken oluşturalım ve bunlara ilk değerlerini tanımlayalım.

```bash
#!/bin/bash

BETIK_ISMI=$0; # $0 programımızın adını taşıyor.
WEBHOOK_URL="https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX";
SLACK_GONDEREN=$(uname -n); #Gönderen belirtilmezse, bilgisayarımızın ağdaki adı gön. ismi olarak görünecek.
SLACK_KANAL="#general"; #Kanal belirtilmezse, #general kanalına gönderilecek.
SLACK_ICON=":desktop_computer:"; #Icon belirtilmezse, :desktop_computer: iconu gösterilecek.
```

Tanımladığımız ilk değişken olan `BETIK_ISMI`, uygulamamızı çalıştırırken kullanacağımız komutun ilk parametresini taşıyor. Bunun açıklamasını, bir sonraki [**Kabuk Betiğine Parametre ile Veri Girişi**](/how-to/slack-webhook/parametre-ile-veri-girisi/) konusunda bulabilirsiniz.

`SLACK_GONDEREN` değişkeni ise aslında gönderen adını tutuyor. Ancak eğer dışarıdan herhangi bir gönderen adı bilgisi girilmezse, `uname -n` komutu sayesinde değer olarak bilgisayarımızın o an ağdaki adını alıyor. Bunun yerine sabit bir gönderen adı da yazılabilir.

`SLACK_KANAL` ve `SLACK_ICON` değişkenlerinde ise, ön tanımlı kanal ve mesaj iconu bilgilerini tutuyoruz.

**Hatırlatma:** Kanal ismini değer olarak girerken ismin başına `#` karakterini eklememiz gerekiyor. Mesajımızı bir kanal yerine, projemize dahil edilmiş bir kullanıcıya özel mesaj olarak göndermek için, kullanıcı adının başına `@` karakterini koymalıyız. (Örn. `"@firat"`)
{: .notice}

**Hatırlatma:** Icon olarak Slack'de tanımlı iconlar yerine, harici bir kaynaktaki görüntüyü kullanmak isterseniz, icon adı yerine görüntünün bağlantısını girin. (Örn. `"https://slack.com/img/icons/app-57.png"`) Ancak bunu yaptığınızda, mesaj kod bloğu içindeki `JSON` yapısında bulunan `icon_emoji` anahtarını, `icon_url` olarak değiştirmelisiniz.
{: .notice}

Böylece boş veri girişine karşı öntanımlı veri barındıran, aynı zamanda dışarıdan parametre olarak girilecek verileri program içindeki komutlarda kullanabileceğimiz değişkenlerimizi tanımladık.

Daha sonra parametre değişkenleri sayesinde dışarıdan girilen verileri  program içine alacağız ve bu verileri değişkenlere atayarak ilgili fonksiyonlarda kullanacağız.