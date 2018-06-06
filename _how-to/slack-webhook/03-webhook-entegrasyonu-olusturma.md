---
title: "Slack Incoming Webhook Entegrasyonu"
permalink: /how-to/slack-webhook/webhook-entegrasyonu-olusturma/
excerpt: "Slack Incoming Webhook Entegrasyonu Oluşturma"
last_modified_at: 2018-05-21T15:19:22-04:00
classes: wide
tags:
  - Slack
  - Webhook
---

Yeni bir Webhook entegrasyonu oluşturmak için, Slack web sitesi üzerinden projemize giriş yaptıktan sonra, [New configuration](https://my.slack.com/services/new/incoming-webhook/) sayfasına giriyoruz. 

Sayfaya giriş yaptığımızda hemen alt satırda bir kanal seçmemiz isteniyor. Bu kanal, göndereceğimiz `JSON` verisinde herhangi bir kanal adı belirtmediğimizde, mesajlarımızın gideceği standart kanal olarak kullanılıyor. Ama `JSON` içinde belirttiğimiz kanalın önceliği var. Yani burada belirttiğimiz kanal ne olursa olsun, mesajımız `JSON` verisi içinde belirteceğimiz  kanala gönderiliyor. Bu alandan isterseniz kanal yerine bir kullanıcının adını da seçebilirsiniz. 

![image-center](/assets/images/how-to/slack-webhook/3-webhook-entegrasyonu-olusturma.png){: .align-center}
{: .notice--primary}

Seçimi yaptıktan sonra hemen altta aktif hale gelen [**Add Incoming WebHooks integration**](#null){: .btn .btn--success} butonuna tıklıyoruz. 

Bu şekilde artık projemizde bir Incoming Webhook entegrasyonu oluşturmuş olduk. İkinci adımda oluşturduğumuz entegrasyonun standart parametrelerini ayarlayarak kaydetmemiz gerekiyor.


