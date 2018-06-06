---
title: "Slack Mesaj Yapısı"
permalink: /how-to/slack-webhook/slack-mesaj-yapisi/
excerpt: "Slack Incoming Webhooks Üzerinden Gönderilen Mesajların Yapısı"
last_modified_at: 2018-05-21T10:20:00-10:00
classes: wide
tags:
  - Slack
  - Webhook
---

Göndereceğimiz mesajların, bir `JSON` yapısı içinde, anahtar / değer çiftleri şeklinde tutulması gerekiyor. Mesaj metnini içeren anahtarı `text` olarak belirtiyoruz. Aşağıda sadece mesaj metnini içeren basit bir `JSON` yapısını görebilirsiniz.

```json
{ "text": "Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır." }
```

**Not:** Mesaj içinde bir alt satıra geçiş yapmak için `\n` karakterlerini kullanıyoruz. `\n` karakterinden sonra yazılan mesaj metni, bir alt satırdan başlatılarak yazdırılır.
{: .notice--info}

Mesajımızı bu haliyle gönderdiğimizde mesaj iletisi gönderilir ancak gönderen ismi ve icon belirtmediğimiz için, bu bilgiler Webhook parametrelerini oluşturduğumuz sırada girdiğimiz ön tanımlı değerlerden alınır. Bu yapıya farklı parametreler ekleyerek, ön tanımlı değerler yerine bizim gönderdiğimiz değerlerin görünmesini sağlayabiliriz.

### Gönderen İsmi

Mesajımızın gönderen adını değiştirmek için `username` anahtarını kullanıyoruz. Bu anahtarı ve içerdiği değeri `JSON` yapımıza dahil edelim.

```json
{
   "username": "firat",
   "text": "Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır."
}
```

### Kanal İsmi / Alıcı Adı

Standart olarak bir kanal belirlemiştik. Farklı bir kanala göndermek istediğimizde `channel` anahtarını kullanıyoruz. Kanal ismini değer olarak girerken ismin başına `#` (diyez) karakterini girmemiz gerekiyor. Mesajımızı bir kanal yerine, projemize dahil edilmiş bir kullanıcıya özel mesaj olarak göndermek için, kullanıcı adının başına `@` (kuruklu a / et) karakterini koyabiliriz. (Örn. `"channel": "@firat"`)

```json
{
   "username": "firat",
   "channel": "#tasarım",
   "text": "Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır."
}
```

**Uyarı:** Webhook entegrasyonunda tanımladığınız ön tanımlı kanal ya da kullanıcı adını, `channel` anahtarına değer olarak yazdığınızda, mesajlarınız `@slackbot` kullanıcısına gönderiliyor. Bu durumu aşmak için, ön tanımlı kanal / kişiye mesaj gönderirken, `channel` anahtarını `JSON` yapısına eklemeyebilir veya değerini boş bırakabilirsiniz (Örn. `"channel": ""`). Bu şekilde gönderilen mesajlar ön tanımlı kanala / kişiye gönderilir.
{: .notice--warning}

### Mesaj Iconu veya Görüntüsü

Her gönderilen mesajın solunda kullanıcıya ait bir görüntü veya icon bulunur. Bu iconu Slack'de tanımlı iconlar üzerinden seçmek için `icon_emoji`, harici bir kaynaktaki görüntüyü seçmek için de `icon_url` anahtarları kullanılır (Örn. `"icon_url": "https://slack.com/img/icons/app-57.png"`).

```json
{
   "username": "firat",
   "channel": "#tasarım",
   "icon_emoji": ":ghost:",
   "text": "Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır."
}
```

### Bağlantı (Link) Oluşturma

Mesaj metni içinde bir bağlantıya köprü oluşturabilmek için `<>` karakterleri içine bağlantıyı eklememiz gerekiyor. Mesaj içinde bağlantı yerine köprülenmiş bir metin göstermek istiyorsak, bağlantının hemen bitimine `|` (pipe) karakterini ekleyip devamında köprü oluşturacağımız metnimizi ekliyoruz.

```json
{
   "username": "firat",
   "channel": "#tasarım",
   "icon_emoji": ":ghost:",
   "text": "Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır. <https://example.com/|Buraya tıklayın.> "
}
```

Şu ana kadar oluşturduğumuz mesaj yapısının, Slack arayüzündeki görüntüsünü aşağıdan görebilirsiniz.

![image-center](/assets/images/how-to/slack-webhook/5-slack-mesaj-yapisi-baglanti.png){: .align-center}
{: .notice--primary}

Mesaj içeriğinde farklı görünümler oluşturmak için [bu sayfaya](https://api.slack.com/docs/message-formatting) göz atabilirsiniz.

Oluşturduğunuz görünümleri [bu sayfada](https://api.slack.com/docs/messages/builder) test edebilirsiniz.

Son oluşturduğumuz haliyle bu yapıyı bir `POST` isteğinin gövdesine ekleyerek gönderebiliriz. İkinci bir yöntem ise bu `JSON` yapısını `POST` istek gövdesinde `payload` parametresine ekleyerek göndermek. Sonraki adımlarda, ikinci yöntemi kullanarak gönderme işlemini yapacağız. Yani elimizdeki verinin son hali aşağıdaki gibi olmalı.

```bash
payload = {
   "username": "firat",
   "channel": "#tasarım",
   "icon_emoji": ":ghost:",
   "text":"Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır. <https://example.com/|Buraya tıklayın.> "
}
```

Yapı bu haliyle sadece mesaj bilgilerimizi içeriyor. `POST` isteğini göndereceğimiz araç olan CURL'e ait bazı parametre ve gereklilikleri de sırası geldikçe kod bloğumuza ekleyeceğiz.