---
title: "Parametreleri Kullanarak Kabuk Betiğine Veri Girişi"
permalink: /how-to/slack-webhook/parametre-ile-veri-girisi/
excerpt: "Bash kabuğu programlarken, parametre ile veri girişi."
last_modified_at: 2018-06-18T17:00:00-10:00
classes: wide
tags:
  - Slack
  - Linux
  - Kabuk Programlama
  - Kabuk Betiği
  - Konumsal Parametre
  - read komutu
---

Çalışmamızın bir önceki bölümünde mesaj metni, kanal ismi ve diğer gerekli verileri doğrudan komut içine yazmış ve komut satırında çalıştırmıştık. Bu defa komutlarımızı bir dosya içine yerleştirdik. Mesaj metni gibi gerekli verileri ise komut içine gömmek yerine, programı her çalıştırdığımızda dışarıdan girebilmemiz gerekiyor. Bunun için kullanabileceğimiz yöntemlere bir bakalım.

## read Komutu

Kabuk programlarında, kullanıcıdan veri almak için kullanılan bir komuttur. Bu komut kabuk tarafından yorumlandığında, sıradaki işlem yapılmadan kullanıcının gireceği veri beklenir. Bu sırada ekranda yanıp sönen bir imleç görünür. Kullanıcı giriş yapana kadar bir sonraki komuta geçiş yapılmaz. Örnek bir kullanımı aşağıda görebilirsiniz.

```bash
#!/bin/bash
echo -n "İsminiz nedir?: "
read isim
echo "Hoş Geldiniz $isim"
```

Bu komutları, read_deneme.sh adında bir dosyaya kaydedip çalıştırdığımızda, aşağıdaki adımlar gerçekleşir.

```bash
$ ./read_deneme.sh
İsminiz nedir?: Fırat Soygül
Hoş Geldiniz Fırat Soygül
```

Görüldüğü gibi dosyayı çalıştırdığımızda ikinci satırda kullanıcıdan giriş yapması beklenir, giriş yapıldıktan sonra da bir sonraki komut çalıştırılır. Aslında göndereceğimiz verileri kullanıcıdan bu şekilde de alabiliriz. Ancak bizim yapmak istediğimiz bu çalışmada mesaj verilerini sadece kullanıcıların değil, diğer uygulamaların da girebilmesini istiyoruz. Bu nedenle verileri girmesi için kullanıcıyla etkileşim kuran ve bekleyen `read` komutu yerine başka bir yöntem kullanacağız.

## Konumsal Parametreler

Bu parametreler, kabuk programı çağrılırken oluşturulur. Bir kabuk programını çalıştırırken girdiğiniz komutun yanına yazılan değerler `$0`, `$1`, `$2`, `$3`... isminde geçici değişkenlere atanır. Bu parametreleri, programın çalışma süresi boyunca uygulama içinde çağırabilir ve kullanabilirsiniz. Örnek olarak kd_deneme.sh adında bir dosya oluşturarak aşağıdaki komutları yazıp kaydedilim.

```bash
#!/bin/bash
echo $0
echo $1
echo $2
echo $3
echo $4
```

Şimdi de oluşturduğumuz bu kabuk betiğini komut satırında çağıralım. Aynı zamanda program ismini yazdıktan hemen sonra, aralarında birer boşluk bırakarak birbirinden farklı bir kaç değer girelim.

```bash
kd_deneme.sh bir iki üç "dört ve beş"
```

Bu komut çalıştırıldığında, betik adının hemen ardına yazdığımız veriler sırasıyla, konumsal parametre olarak bazı geçici değişkenlere atanıyor ve kabuk betiği çalıştırılıyor. Bu noktadan itibaren betik içinde bu değişken isimleri çağrılarak içerdikleri değerler kullanılabilir. Örneğimizde bu değişkenlere atanan değerleri, `echo` komutuyla ekrana yazdırıyoruz. Ekran çıktısını aşağıdan görebilirsiniz.

```bash
$ ./kd_deneme.sh bir iki üç "dört ve beş"
./kd_deneme.sh
bir
iki
üç
dört ve beş
```

Görüldüğü gibi betik çalıştırılırken komut satırından yazılan veriler, sıralı bir şekilde konumsal parametre olarak atanmış durumda. Ancak `$1` değişkeni girilen ilk parametreyi tutarken, `$0` değişkenine ise çağırdığımız kabuk programının çalıştırıldığı kod bloğu atanmış. Yani komut satırına girdiğimiz bütün değerler, aralarındaki boşluk karakteri ile ayrılarak soldan sağa sıralı olarak konumsal parametre olarak belirlenmiş durumda. Bu durumu altta gösterildiği gibi düşünebiliriz.

```bash
kd_deneme.sh bir iki üç "dört ve beş" ....
------------ --- --- -- ------------- ----
$0           $1  $2  $3 $4            ${N}
```

Bu yöntemi kullanırken dikkat edilmesi gereken iki önemli kural var.

- Eğer girilen parametre miktarının basamak sayısı tek sayılı haneleri geçerse, parametreyi kullanabilmek için parametre numarasını küme parantezleri `{` `}` içine alarak yazmamız gerekiyor (Örn. `${10}`, `${16}`).

- Veri girişini yaparken, aradaki boşlukların ayırıcı özelliklerini iptal etmek ve birden fazla kelimeyi tek bir parametreye atamak için, veriyi çift tırnak `"` karakterleri arasına yazmalısınız.

Verilerimizi bu şekilde betik içine çekebiliriz ancak hala kullanım bakımından bir zorluk söz konusu. Girişini yapmamız gereken mesaj metni, kullanıcı ismi gibi birden fazla değer var ve girdiğimiz verilerin hangisinin hangi veri türüne karşılık geldiğini bu şekilde anlamak biraz zor. 

CURL kullanımında hatırlarsanız, CURL aracının kendi adını girdikten sonra sırasıyla parametrelerimizi ve atadığımız değerleri arka arkaya giriyorduk.

```bash
curl -X POST -H "Content-type: application/json"
```

Bu giriş yönteminde uygulamanın adından (`curl`) sonra gelen `-X` ifadesi, CURL aracına göndereceğimiz konumsal parametrenin anahtarı olarak kullanılıyordu. Anahtardan sonra gelen `POST` ise parametreye atadığımız değer olarak giriliyor. Bir sonraki `-H` anahtarı da yine bir başka parametre anahtarı ve `Content-type: application/json` da bu parametreye ait değer olarak giriliyor. Yani bir verinin girişini yaparken öncelikle o verinin türünü `-` karakteri ile kısaca tanıtıyor, hemen sonrasında ise o türe ait değeri giriyoruz. Böylece uygulama kendisine gelen verilerin türlerini yaptığımız bu tanımlamadan anlayarak gerekli atamaları yapıyor.

Buna benzer bir parametre girişini kendi yazdığınız bir fonksiyonla da yapabilirsiniz elbet. Ancak hali hazırda Bash kabuğunda bu işi yapmak için geliştirilmiş bir fonksiyon zaten mevcut. `getopts` adı verilen bu fonksiyonu [bir sonraki sayfada](/how-to/slack-webhook/getopts-fonksiyonu/) inceleyebilirsiniz.