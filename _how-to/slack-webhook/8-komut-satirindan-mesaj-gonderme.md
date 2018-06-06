---
title: "Komut Satırından Slack Mesajı Gönderme"
permalink: /how-to/slack-webhook/komut-satirindan-mesaj-gonderme/
excerpt: "CURL aracını kullanarak Linux komut satırından Slack mesajı gönderme işlemi."
last_modified_at: 2018-05-24T19:20:00-10:00
classes: wide
tags:
  - Slack
  - Webhook
  - Linux
  - Kabuk programlama
  - Kabuk betiği
  - JSON
---


Mesaj yapımızı oluşturduk ve Curl parametrelerimizle birlikte, Webhook URL'e gönderebilir durumdayız. Bu aşamada kod bloğumuzu, komut satırında işlenebilir hale getireceğiz. Bunun için sıkça kullanacağımız ters eğik çizgi `\` hakkında biraz bilgi sahibi olmamız gerekiyor.

Linux komut satırında ters eğik çizgi `\`, kendisinden sonra gelen karakteri işlevsizleştiren bir parametredir(`\n` kullanımı hariç). Aşağıda gördüğünüz kod, daha okunabilir olması için bir kaç satırda yazılmış. Ve bir alt satıra geçmek için de `enter` tuşu kullanılıyor. Fakat komut satırında enter tuşuna basıldığında komutu çalıştırmaya başlar. İşte bu nedenle, kod içindeki enter komutlarını etkisiz hale getirmek için, her enter komutundan önce bir ters eğik çizgi konuldu. 

```bash
$ curl -X POST \
--data-urlencode "payload = { \
   "username": "firat", \
   "channel": "#tasarım", \
   "icon_emoji": ":ghost:", \
   "text": "Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır." }" \
https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX
```

Bu işlem sadece kodların daha okunabilir olması amacıyla yapıldı. Siz bütün bu komutları, aşağıdaki gibi alt satıra geçmeden tek satır halinde de yazabilirsiniz.

```bash
$ curl -X POST --data-urlencode "payload = { "username": "firat", "channel": "#tasarım", "icon_emoji": ":ghost:", "text":"Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır." }" https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX
```

Ters eğik çizginin `\`, kendisinden sonra gelen karakteri yürütülebilir bir kod parçası olarak değil, işlevsiz bir string karakter olarak kullanılmasını sağladığını belirtmiştik (\n kullanımı hariç). Diyelim ki gönderen adının içinde çift tırnak işareti `"` kullanmamız gerekiyor. Örneğin ismimin içindeki `r` harfi yerine çift tırnak işareti kullanmak istiyorum. Adımı fi"at şeklinde göndermeliyim. Dolayısıyla kodu `"username": "fi"at"` şeklinde yazmalıyım. Ancak bu durumda bir problem oluşuyor. İsmimin içinde kullanmak istediğim `"` işaretini gören komut satırı bunu ismimin bir parçası olan string karakter gibi değil de, string bloğu başlatan ve bitiren bir parametre olarak yorumlayacak ve sadece tırnağa kadar olan "fi" kısmını değer olarak alacaktır. İşte bu sorunu aşmak için ters eğik çizgiyi kullanarak `"` işaretinin komut işlevini yitirmesini sağlayabiliyoruz. `"username": "fi\"at"` şeklinde yazılan bir kod parçacığında, taşınan `username` değeri `fi"at` şeklinde oluyor ve ters eğik çizgi kendini imha ediyor yani çıkan sonuçta görünmüyor. 

Bizim oluşturduğumuz kod bloğunda da buna benzer bir durum var. Dikkat ettiyseniz en içte bir `JSON` yapımız, bu yapıyı taşıyan `payload` adında bir anahtarımız ve bu anahtarı da taşıyan `--data-urlencode` adında bir Curl parametremiz var. 

![image-center](/assets/images/how-to/slack-webhook/8-payload.png){: .align-center}
{: .notice--primary}

`--data-urlencode` parametresinin veri bloğunu başlatıp bitiren birer çift tırnak varken, `payload` anahtarının taşıdığı `JSON` yapısının da kendi değer taşıyıcı tırnakları var. Yani iç içe geçmiş tırnaklar söz konusu. İşte bu noktada komut satırının, içeride bulunan ve Webhook'a göndereceğimiz `JSON` yapımızdaki çift tırnak işaretlerini komut olarak görmemesini, sadece birer string ifade gibi yorumlamasını sağlamak için, iç blokta bulunan tırnakların hepsinin önüne ters eğik çizgi `\` koymamız gerekiyor. Çünkü iç blokta bulunan `JSON` yapısına ait çift tırnak karakterleri, komut satırı tarafından değil Webhook tarafında verimizi karşılayacak olan API içinde işlenmeli.

```bash
$ curl -X POST \
--data-urlencode "payload = { \
   \"username\": \"firat\", \
   \"channel\": \"#tasarım\", \
   \"icon_emoji\": \":ghost:\", \
   \"text\": \"Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır.\" }" \
https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX
```

Komut bu haliyle, geçerli bir Webhook URL tanımlayıp, komut satırında çalıştırılabilir (Kod içindeki URL sunum amaçlıdır, kendi URL adresinizle değiştirmeniz gerekiyor). Aynı kodu bir de tek satır olarak görelim.


```bash
$ curl -X POST --data-urlencode "payload = { \"username\": \"firat\", \"channel\": \"#tasarım\", \"icon_emoji\": \":ghost:\", \"text\": \"Bu bir deneme mesajıdır.\nBu cümle ise ikinci satıra yazılır.\" }" https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX
```

Ve sonunda Webhook ile mesaj gönderebileceğimiz komut satırı kodumuz hazır. Kendi Webhook URL'inizi koda yerleştirin, mesaj ve gönderen bilgilerini düzenleyin. Artık komut satırına girerek mesajınızı gönderebilirsiniz.

![image-center](/assets/images/how-to/slack-webhook/2-webhook-ornek-mesaj.png){: .align-center}
{: .notice--primary}



