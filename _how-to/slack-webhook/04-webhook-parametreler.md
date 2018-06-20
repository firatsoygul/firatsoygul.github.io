---
title: "Slack Incoming Webhook Ön Tanımlı Parametreler"
permalink: /how-to/slack-webhook/webhook-parametreler/
excerpt: "Slack Incoming Webhook Ön Tanımlı Parametreler"
last_modified_at: 2018-05-21T15:19:22-04:00
classes: wide
tags:
  - Slack
  - Webhook
---

Bir önceki adımda yeni bir Incoming Webhooks entegrasyonu oluşturmuş ve standart kanalımızı seçmiştik. Bu işlemden sonra açılan **Edit Configuration** sayfasında bazı parametreleri belirlememiz gerekiyor.

{% capture notice-text %}
![image-right](/assets/images/how-to/slack-webhook/4-custom-integration.png){: .align-right}
**Kısayol:** New configuration sayfasını kapattıysanız, sayfaya geri dönebilmek için:

* [Custom Integration](https://my.slack.com/apps/manage/custom-integrations) sayfasına girin.  
* Yandaki **Incoming Webhooks** yazısına tıklayın.  
* Açılan sayfada **Configured Custom Integrations** başlığı altındaki listede Webhook adınızı bulun ve yanındaki kalem simgesine tıklayın.  

Eğer hiç bir isim görüntülenmiyorsa oluşturma işlemi tamamlanamamış demektir. Bu nedenle bir önceki adımı tekrar uygulayarak önce yeni bir entegrasyon oluşturmanız gerekiyor. Sonra bu adımdan devam edebilirsiniz.
{% endcapture %}

<div class="notice">
  {{ notice-text | markdownify }}
</div>

Bu sayfada **Setup Instructions**, **Message Attachments** ve **Integration Settings** adında üç ana başlık bulunuyor.

### Setup Instructions

 Bu başlık altında Incoming Webhooks URL'si ve genel bir mesaj yapısının nasıl olması gerektiğine dair bazı bilgiler ve örnekler verilmiş. Mesajınıza bağlantı içeren metinler eklemek, gönderen adı veya iconunu değiştirmek için gerekli işlemler örneklerle gösterilmiş. Ve Curl kullanılarak örnek bir `POST` isteği komut yapısı verilmiş.

### Message Attachments

Bir önceki adımda not olarak belirttiğim zengin formatlı mesaj içeriklerinin nasıl oluşturulacağı hakkında bilgiler bu başlık altında bulunuyor.

### Integration Settings

Bu başlık altında ise ön tanımlı parametrelerimizi belirleyeceğimiz alanlar var.

* **Post to Channel** alanında, bir önceki adımda belirlediğimiz standart kanalımız görünüyor. Bu alanı kullanarak kanalı değiştirebiliyoruz.
* **Webhook URL** alanında görülen linki bir yere yedekleyelim. İlerleyen aşamalarda bu linke oluşturduğumuz mesaj bloklarını göndereceğiz.
* **Descriptive Label** alanı, oluşturulan çok sayıda Webhook arasından istediklerimizi daha kolay ayırt edebilmemiz için kullanılan etiketlerin girildiği alan. İsteğe bağlı olduğu için boş bırakabilirsiniz.
* **Customize Name** alanına, mesajımızın standart gönderen ismini giriyoruz. Eğer gönderilen mesajda herhangi bir kullanıcı adı belirtilmezse mesajımız bu isimle gönderilecek.
* **Customize Icon** alanında, mesajımızın solunda beliren icon seçilebiliyor. Bu alanda ister Slack içinde kullanılan iconlardan birini seçebilir, isterseniz de başka bir görüntü dosyası yükleyebilirsiniz. Bu bilgi de yine ön tanımlı olduğu için uygulama içinde göndereceğimiz icon bilgisi bu bilginin yerine geçecek.
* **Preview Message** alanında mevcut parametrelerle mesajınızın Slack üzerinde nasıl görüntüleneceğine dair örnek bir görüntü verilmiş.

Parametreleri ayarladıktan sonra [**Save Setting**](#null){: .btn .btn--success .btn--small} butonu ile kaydedelim.

Artık projemiz üzerinde kullanabileceğimiz bir Incoming Webhooks entegrasyonumuz var ve bu entegrasyonda oluşturulan bağlantıya mesaj bloğumuzu göndererek Slack üzerinde yayınlanmasını sağlayabiliriz.