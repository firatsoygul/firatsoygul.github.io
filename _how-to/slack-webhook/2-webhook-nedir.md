---
title: "Slack Incoming Webhook Nedir?"
excerpt: "Slack Incoming Webhook nedir ne işe yarar?"
permalink: /how-to/slack-webhook/incoming-webhook-nedir/
classes: wide
tags:
  - Slack
  - Webhook
last_modified_at: 2018-05-20T15:19:22-04:00
---

Webhook, belirli bir web adresi üzerinden `HTTP` istekleri gönderilerek yapılan veri iletişimidir. Bu adrese `POST` veya `GET` yöntemi ile bir `HTTP` isteği gönderilir. İsteği alan uygulama gerekli işlemi yerine getirir. Eğer gerekli ise aynı yol ve yöntemlerle isteği yapan uygulamaya yine bir `HTTP` isteği gönderebilir. Slack platformunda Slack tarafına yapılan `HTTP` istekleri için [Incoming Webhooks](https://api.slack.com/custom-integrations/incoming-webhooks), Slack tarafından istemciye yapılan istekler için de [Outgoing Webhooks](https://api.slack.com/custom-integrations/outgoing-webhooks) entegrasyonunun yapılması gerekir. Bu uygulamada ise Slack Incoming Webhooks kullanarak, yazdığımız mesajların Slack projemizde bulunan kanal ya da kişilere mesaj olarak gönderilmesini sağlıyoruz.

**Not:** Eğer Slack "gelen" mesajlarını uygulamanız üzerine çekmek istiyorsanız [Outgoing Webhooks](https://api.slack.com/custom-integrations/outgoing-webhooks) entegrasyonu yapmalısınız.
{: .notice--info}

Bu yöntemde mesaj metni, gönderen ismi, gönderilen kanal ya da kişi ve mesaj ikonu gibi bazı verileri üzerinde taşıyan, [JSON](http://json.org/json-tr.html) formatında bir yapı oluşturulur ve `HTTP` protokolü üzerinden `POST` yöntemi ile gönderilir. Datanın gönderilmesiyle birlikte mesajımız Slack projemizde gerçek zamanlı olarak belirecektir. 

![image-center](/assets/images/how-to/slack-webhook/2-webhook-ornek-mesaj.png){: .align-center}
{: .notice--primary}

Bu şekilde gönderilen mesajların, görünüm bakımından diğer mesajlardan farkı yoktur. Yine bir gönderen adı, saat, gönderene ait bir icon ve mesaj metni bulunmaktadır. Yalnızca gönderen adının yanında api aracılığıyla gönderildiğini ifade eden bir `APP` ibaresi görülür. 

**Not:** Farklı konu satırları ve butonlar içeren, görsel bakımdan daha zengin iletiler göndermek isterseniz [bu sayfayı](https://api.slack.com/docs/message-attachments) inceleyebilirsiniz.
{: .notice--info}

Bu yöntemi kullanarak mesaj gönderebilmemiz için ilk olarak Slack üzerinde yeni bir Incoming Webhooks entegrasyonu oluşturmamız gerekiyor. Daha sonra bu entegrasyonun başlangıç parametrelerini belirleyerek, oluşturulan linki uygulamamızda kullanmak üzere bir yere yedeklemeliyiz.




