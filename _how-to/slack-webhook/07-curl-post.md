---
title: "CURL ile POST İsteği"
permalink: /how-to/slack-webhook/curl-post/
excerpt: "CURL aracı ile POST isteği gönderme ve gerekli parametreler."
last_modified_at: 2018-05-22T16:20:00-10:00
classes: wide
tags:
  - Slack
  - Webhook
  - CURL
  - Linux
  - POST
---

Curl aracı ile POST isteği gönderirken kullanmamız gereken bazı parametreler var. Bunlar `-X`, `-H`, `-d` ve `--data-urlencode` parametreleri. Ayrıca Webhook entegrasyonu oluşturma bölümünde not aldığımız Webhook URL'ini de bu aşamada CURL aracına girmemiz gerekiyor. Şimdi bu parametrelere ve kodumuzun değişen yapısına bakalım.

### -X veya - -request

Bu parametre ile verimizin hangi yöntemle gönderileceğini belirliyoruz. Slack Incoming Webhooks `POST` yöntemini desteklediği için `-X POST` parametresini Curl komutuna dahil etmemiz gerekiyor. Bu parametre yerine `--request` de kullanabilirsiniz(Örn: `--request POST`). İki parametre de aynı işlemi yapacaktır. Parametreyi Curl aracına aşağıdaki gibi tanımlıyoruz.

{% highlight bash %}
$ curl -X POST
{% endhighlight %}

veya

{% highlight bash %}
$ curl --request POST
{% endhighlight %}

Ancak komut bu şekilde çalışmayacaktır. Curl, URL'in belirtilmediğine dair bir hata mesajı döndürür. Ayrıca mesaj verimizi de eklemeliyiz.

### -H veya - -header

POST ile veri gönderirken kullanılabilen iki format var.

* `application/json`
* `application/x-www-form-urlencoded`

Veriyi gönderirken veri formatımızı `-H` veya `--header` parametrelerini kullanarak belirtmemiz gerekiyor. Bu parametrelerden birini ve hemen ardından formatımızı yazarak Curl aracına aşağıdaki gibi tanımlıyoruz.

```bash
curl -X POST -H "Content-type: application/json"
```

Veya `application/x-www-form-urlencoded` formatı için komutu aşağıdaki gibi yazıyoruz.

```bash
curl -X POST -H "Content-type: application/x-www-form-urlencoded"
```

### -d veya - -data

Göndereceğimiz verileri belirttiğimiz parametredir.

Sunucuya `POST` ile gönderilen veriler, `HTTP` istek gövdesinde saklanır. Bizim gönderdiğimiz veri ise `JSON` yapısında. Ayrıca mesajınızda daha zengin içerikler sunabilmeniz için, üzerinde `link` ve `button` gibi `HTML` form verilerinin eklenmesi de sağlanmış. Bu tür veriler sunucuya gönderilmeden önce URL kodlaması (yüzde kodlaması) denilen bir yöntemle kodlanıyor. URL kodlaması, bir URL'deki `?`, `/` ve `=` gibi özel karakterleri `%3F`, `%2F` ve `%3D` gibi kodlara dönüştürür. Bu kodlama işlemi belirsizliği ortadan kaldırıyor.

Bu parametre ile girilen veriler ise kodlanmaz. Eğer verilerimizi göndereceğimiz API, veriyi kodlamamızı zorunlu kılıyorsa, bu parametreye gireceğimiz verinin daha önceden kodlamasının yapılmış olaması gerekiyor. Eğer kodlama gerektirmeyen veya doğrudan `JSON` formatlı verileri göndermek istiyorsak, bu parametre ile kodlamadan gönderebiliyoruz.

Son oluşturduğumuz mesaj bloğumuz aşağıdaki gibiydi. `JSON` yapısını `payload` parametresine eklemiştik.

```bash
payload = {
   "username": "firat",
   "channel": "#tasarım",
   "icon_emoji": ":ghost:",
   "text":"Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır. <https://example.com/|Buraya tıklayın.>"
}
```

Eğer verimizi `application/json` formatında göndermek isteseydik, `payload` parametresini çıkartmamız, istek yöntemini ve veri formatımızı da aşağıdaki gibi belirtmemiz gerekecekti.

```bash
curl -X POST -H "Content-type: application/json"
--data "{
   "username": "firat",
   "channel": "#tasarım",
   "icon_emoji": ":ghost:",
   "text":"Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır. <https://example.com/|Buraya tıklayın.>"}"
```

Ancak biz verimizi daha önce kodlamadığımız ve kodlama işleminin Curl tarafından yapılmasını istediğimiz için, `-d` veya `--data` parametresini kullanmayacağız.  `HTML-form` tipindeki özel karakterleri de taşıyabileceğimiz `application/x-www-form-urlencoded` formatını kullanacağız ve Curl'in `--data-urlencode` parametresini kullanarak, `payload` anahtarına yükleyerek göndereceğiz.

### - -data-urlencode

Eğer URL kodlama işlemini önceden yapmış olsaydık, `--data`, `--data-binary` gibi parametrelerle veriyi gönderebilirdik. Ancak bizim göndereceğimiz veri daha önceden kodlanmadığı için `--data-urlencode` parametresi ile veri içeriğimizin Curl tarafından kodlanmasını sağlayabiliyoruz.

`HTML-form` tipinde özel karakter içerme potansiyeli olan verimizi, aşağıdaki gibi `application/x-www-form-urlencoded` formatıyla belirterek oluşturabiliriz.

```bash
curl -X POST -H "Content-type: application/x-www-form-urlencoded"
--data-urlencode "payload = {
   "username": "firat",
   "channel": "#tasarım",
   "icon_emoji": ":ghost:",
   "text":"Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır. <https://example.com/|Buraya tıklayın.>" }"
```

Görüldüğü gibi `-X` parametresi ile gönderim yöntemini, `-H` parametresi ile de verimizin formatını belirtiyoruz. Eğer herhangi bir veri formatı belirtilmezse Curl veriyi ön tanımlı olarak `application/x-www-form-urlencoded` olarak kabul ediyor. Dolayısıyla, eğer `HTML-form` tipinde karakterlerin bulunduğu bir veri gönderiyorsak, ayrıca bir de `-H` parametresi ile format belirtmemize gerek kalmıyor. Yani komutumuzdan `-H` parametresini ve içerdiği değeri çıkartarak aşağıdaki gibi yazdığımızda da amacımıza ulaşmış oluyoruz.

```bash
curl -X POST
--data-urlencode "payload = {
   "username": "firat",
   "channel": "#tasarım",
   "icon_emoji": ":ghost:",
   "text":"Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır. <https://example.com/|Buraya tıklayın.>" }"
```

**Öneri:** Slack, yukarıda bahsedilen iki formatı da kabul ediyor ve ikisi ile de nasıl gönderim yapılabileceğine dair bilgileri [sayfalarında bulabilirsiniz](https://api.slack.com/incoming-webhooks). Fakat amacınız komut satırını kullanarak gönderim yapmaksa, ek URL kodlaması gerektirmeyen ve daha kısa olan ikinci yöntemi seçmemiz daha mantıklı olacaktır.
{: .notice}

Son parametre olarak da, daha önce yedeklediğimiz **Webhook Url** değerimizi Curl aracına giriyoruz.

Bütün bu işlemlerden sonra kod yapımızın son halini aşağıda görebilirsiniz.

```bash
curl -X POST
--data-urlencode "payload = {
   "username": "firat",
   "channel": "#tasarım",
   "icon_emoji": ":ghost:",
   "text":"Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır. <https://example.com/|Buraya tıklayın.>" }"
https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX
```

Mesaj gönderme işlemini komut satırından yapacağımız için kod üzerinde karışıklıkları önlemek adına yapmamız gereken bazı eklemeler var. Bunları bir sonraki  [**Komut Satırından Mesaj Gönderme**](/how-to/slack-webhook/komut-satirindan-mesaj-gonderme/) konusu altında bulabilirsiniz.
