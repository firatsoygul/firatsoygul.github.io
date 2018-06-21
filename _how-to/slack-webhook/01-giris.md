---
title: "Slack Webhooks ile Linux Komut Satırından Mesaj Gönderme"
permalink: /how-to/slack-webhook/baslangic/
excerpt: "Gömülü sistemlerle uğraşıyorsanız, komut satırından Slack platformuna ulaşmak isteyebilirsiniz. Ya da IOT donanımlarınızdaki hareketlerin takibi için Slack platformunu kullanmak isteyebilirsiniz."
last_modified_at: 2018-05-20T15:19:22-04:00
classes: wide
categories: 
  - Nasıl Yapıldı
tags:
  - Slack
  - Webhook
  - Linux
  - Kabuk programlama
  - Kabuk betiği
header:
  teaser: "/assets/images/how-to/slack-webhook/slack-header.jpg"
---

Proje geliştirirken, ekip içindeki iletişimi kolaylaştıran platformlardan biri de Slack. Slack üzerinde oluşturduğumuz bir proje alanı sayesinde, ekip üyeleriyle iletişim kurabiliyor ve gerekli etkileşimi sağlayabiliyoruz. Slack platformunu kullanabileceğiniz uygulamalar zaten kendileri tarafından sağlanmış durumda. Mobil tarafında Android ve IOS, masaüstü tarafında ise Linux, Windows ve Mac uygulamalarını sitelerinden indirip kullanabiliyorsunuz. Eğer gömülü sistem alanında uğraşıyorsanız, bu uygulamaların kurulamadığı ortamlardan da Slack platformuna ulaşmak isteyebilirsiniz. Ya da IOT donanımlarınızdaki hareketlerin takibi için Slack platformunu kullanmak isteyebilirsiniz. Örneğin iş yerinizdeki sıcaklık sensörlerinin bağlı olduğu bir IOT donanımdan, belli bir ısının alt ve üst seviyelerinde size mesaj göndermesini isteyebilirsiniz. İşte bu gibi durumlarda, Linux çekirdeği üzerinde çalışan kabuğu programlayıp, belirli görevlerde Slack projenizdeki bir kanala ya da kullanıcıya mesaj olarak bilgi göndermesini sağlayabilirsiniz.

### Adımlar

- İlk aşamada, Slack platformunun bize sağladığı **Slack Webhook** yöntemini kullanabilmek için gerekli entegrasyonu oluşturuyor ve bu entegrasyon üzerinden bağlantı kurmamızı sağlayacak olan CURL aracını yapılandırıyoruz.

- Linux işletim sistemi üzerinde Bash kabuğunu kullanarak, önceki adımda  oluşturduğumuz entegrasyon bilgileri üzerinden projemizle bağlantı kuruyor ve mesajımızı gönderiyoruz.

- Buraya kadar yaptığımız işlemleri kabuk betiğine çevirip, çalıştırılabilir bir uygulama haline getiriyoruz ve parametreler ile komut satırından girişini yaptığımız içeriğin mesaj olarak gönderilmesini sağlıyoruz. Oluşturduğumuz bu kabuk betiğini artık, gömülü Linux işletim sistemi kullanan herhangi bir donanıma yükleyip tetikleyerek, istediğimiz verilerin gönderilmesini sağlayabiliriz.

- Ve son olarak bu betiği, kabuk üzerinde çalışan bir grafik kullanıcı arayüzü scriptine entegre ediyoruz. Böylece masaüstü ortamı yüklü bir Linux sistemde mesaj bilgilerinin kullanıcıdan grafik arayüzü ile alınmasını ve gönderilmesini sağlıyoruz.

**Bilgi:** Çalışma, içerdiği konu başlıklarına göre sayfalara ayrıldı. Bu sayfalara soldaki geçiş menüsünü kullanarak ulaşabilirsiniz. Bu sayfaları sıralı şekilde takip ettiğinizde, aklınızda çok fazla soru işareti bırakmadan çalışmayı tamamlayabileceğinizi umuyorum.
{: .notice--success}

### Gereksinimler

Bu uygulamayı sonuca ulaştırabilmek için aşağıdakilere ihtiyacınız olacak.

- Linux İşletim Sistemi (Gerçek ya da sanal olması fark etmez. Ayrıca Windows üzerinde çalışan Linux kabuklarını da, son bölümdeki GUI kullanımı hariç kullanabilirsiniz).
- BASH kabuğu.
- CURL URL veri aktarım aracı.
- YAD GUI script.
- Yönetim haklarına sahip olduğunuz bir Slack projesi.

Burada adı geçen araç ve scriptlerin kurulması ve kullanılması hakkındaki bilgilere, sırası geldikçe yer verilecektir.