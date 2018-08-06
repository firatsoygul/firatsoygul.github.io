---
title: "GETOPTS Fonksiyonu ve Konumsal Parametreler"
permalink: /how-to/slack-webhook/getopts-fonksiyonu/
excerpt: "Bash kabuğu programlarken, parametre ile veri girişi."
last_modified_at: 2018-06-18T20:20:00-10:00
classes: wide
tags:
  - Slack
  - Linux
  - Kabuk Programlama
  - Kabuk Betiği
  - getopts
  - Konumsal Parametre
---

Komut dosyasına parametre olarak girilen veriler, okunabilirliğin artırılması adına tür belirten anahtarlarla birlikte aktarılabilir. Örneğin mesaj metnimizi girmeden önce metnin başına '-m', kanal ismini girmeden önce de başına '-k' anahtarlarını koyabiliriz. Bu durumda hem giriş esnasında oluşabilecek karışıkları önlemiş hem de komut dosyası içinde bu değerleri karşılaştırmamız kolaylaşmış olur.

Çalışmamızda, kullanıcının komut dosyasına dışarıdan girilebileceği mesaj metni, kanal adı gibi bazı değerler var. Bu değerlerin girişini yaparken her değerin türünü bir ön anahtarla belirtelim. Bu değerler ve türleri aşağıdaki gibi oluşturabiliriz.

- `-g` Gönderenin ismi.
- `-k` Gönderilen kanal veya kullanıcının adı.
- `-i` Kullanıcı iconu veya resmi.
- `-m` Mesaj metni.
- `-h` Yardım.

Bu şekilde belirlediğimiz bir parametre anahtar listesiyle, komut dosyamızı daha okunabilir bir komut dizesiyle başlatabiliriz. Örnek kullanım için aşağıdaki komutu inceleyiniz.

```bash
slackCLI.sh -g "Fırat SOYGÜL" -k @ahmet -m "Mesaj metni."
```

Görüldüğü gibi her verinin başında onun türünü belirten bir anahtar var. Aslında bu yazım düzeninde de yine her komut bloğu bir parametre olarak kabul edilerek, `$0`, `$1`, `$2`, `$3` şeklinde geçici değişkenlere atanıyor. Bütün bu parametrelere `$@` **dizi değişkeninden** ulaşılabiliyor. Yani `$@` dizi değişkenini bir for döngüsüne atayarak, her döngüde parametre değerinin yukarıda oluşturduğumuz tür listesine uyup uymadığını bir sorguyla karşılaştırıp, eğer uyan bir tür değeri görülürse sonraki değerin o türe göre işlem görmesini sağlayabiliriz. Ancak Bash kabuğunun bu işlemi yapabilmemiz için bize sağladığı `getopts` adında bir fonksiyonu var. Çok daha pratik olacağı için biz de çalışmada bu fonksiyonu kullanacağız.

