---
title: "Linux'ta Kullanıcının Ön Tanımlı Kabuğu Nasıl Değiştirilir?"
permalink: /article/linux-on-tanimli-kabuk-degistirme/
excerpt: "Linux işletim sisteminde ön tanımlı olarak yüklenen kabuk uygulaması nasıl değiştirilir?"
date: 2018-05-25
last_modified_at: 2018-05-25T15:58:49-04:00
categories: 
  - Makale
tags:
  - Linux
  - Shell
  - Kabuk programlama
  - Bash
  - chsh
  - usermod
header:
  teaser: "/assets/images/unsplash-gallery-image-2.jpg"
toc: true
toc_sticky: true
---

Linux'ta bir kullanıcı oluşturulduğunda, o kullanıcıya bir kabuk tanımlanır. Sistem yöneticisi, kullanıcı için varsayılan bir kabuk belirlemiş de olabilir. Kullanıcı daha sonra, kendisine tanımlanmış ön tanımlı kabuğunu geçersiz kılarak başka bir kabuk çalıştırmayı seçebilir.

**Hatırlatma:** Kabuk, kullanıcı ile işletim sistemi çekirdeği arasında iletişim kuran, metin olarak verilen komutları yorumlayan yardımcı bir programdır. Dosya işlemleri(yazma, okuma, silme, listeleme vb.), sistemde çalışan işlem parçalarını listeleme veya müdahale etme gibi işlerin kontrolünün ve takibinin yapılmasını sağlar. Buna benzer ön tanımlı işlemlerin komutlarını sağladığı gibi, sonradan sisteme dahil edilmiş programların gereksinim duyduğu komutları da sistem çekirdeğine gönderir.
{: .notice}

**Not:** Linux ortamında çalışan [bash](https://www.gnu.org/software/bash/bash.html), csh, sh, [ksh](http://www.kornshell.org/), [tcsh](https://github.com/tcsh-org/tcsh), [zsh](http://www.zsh.org/), [fish](https://fishshell.com/) gibi bir çok kabuk programı var. Bunların içinde en popüler olanı bash kabuğudur ve çoğu dağıtımda ön tanımlı olarak gelir. Sisteminize birden fazla kabuk kurabilir ve bunların ön tanımlı olarak çalışma durumunu değiştirebilirsiniz.
{: .notice--info}

Kullanıcı, kabuğu değiştirmeden önce mevcut kabuğun ne olduğunu ve değiştirmek istediği kabuğun sistemde yüklü olup olmadığını öğrenmek isteyebilir. Bu işlemler için [**mevcut kabuğu öğrenme**](#mevcut-kabuğu-öğrenme) başlığını, kabuk değiştirme konusu için de [**ön tanımlı kabuğu değiştirme**](#ön-tanımlı-kabuğu-değiştirme) başlığını okuyabilirsiniz.

## MEVCUT KABUĞU ÖĞRENME

Linux sistemlerde sıkça kullanılan bazı değerler sistem değişkeni olarak adlandırılan yapılarda barındırılır. Ön tanımlı bir çok değer, sistem açılışında bu değişkenlere aktarılır ve sistemin çalıştığı süre içinde buraya başvuran herhangi bir işlem tarafından kullanılabilir. İhtiyacınız olan bilginin tutulduğu sistem değişkeninin içeriğini ekrana yazdırarak, sisteminizdeki ön tanımlı değerini görüntüleyebilirsiniz. Değişkenleri ekrana yazdırmak için `echo` komutunu kullanabilirsiniz.

### `$SHELL` Sistem Değişkeni

Sisteminizde ön tanımlı olarak belirlenmiş kabuğun çalıştırılabilir dosya yolunu, `$SHELL` sistem değişkenine bakarak öğrenebilirsiniz.

```sh
$ echo $SHELL
/bin/bash
```

### `$0` Bağımsız Değişkeni

Ön tanımlı kabuk değiştirildiğinde bir sonraki sistem açılışında aktif olur. Eğer ön tanımlı kabuğu değiştirdiyseniz ve sistemi henüz yeniden başlatmadıysanız, `$SHELL` değişkeni size yeni belirlediğiniz değeri döndürecektir. Ancak o anda hala geçerli kabuk değişimden önceki kabuktur. Bu bilgiye ulaşmak için de `$0` değişkenine bakabilirsiniz. `$0` değişkeni, çalışan kabuk veya kabuk betiğinin çağrıldığı komutu taşır. Dolayısıyla bir kabuk çalışırken bu değişken, çalışan kabuğun çağrıldığı komutu taşıyacaktır. Bu komut da genelde, kabuğun adı olmaktadır.

```sh
$ echo $0
fish
```

### `/etc/shells` Dosyası

Diyelim ki sisteminizde ön tanımlı kabuk olarak csh var ve siz bunu bash ile değiştirmek istiyorsunuz. Değiştirme işleminden önce bash kabuğunu sisteminize yüklemiş olmanız gerekiyor. Ön tanımlı kabuğu değiştirmeden önce, gerekli kabuğa sahip olduğunuzdan emin olmanız gerekir.

Kabuklar genellikle `/bin` dizini altına yüklenir. Ancak farklı Linux dağıtımlarında bu durum değişebilir. Bazı dağıtımlarda `/usr/bin` veya `/usr/local/bin` gibi farklı konumlara da yüklenmiş olabilir. Bu nedenle ön tanımlı yapacağınız kabuğun kurulu olduğu dizini de öğrenmeniz gerekli.

Sisteminizde hangi kabukların yüklü olduğu bilgisi, `/etc` dizini altındaki `shells` dosyasında tutulur. Bu dosyanın içeriğini, `cat` komutunu kullanarak ekrana yazdırabilirsiniz. `cat /etc/shells` komutunu girdiğinizde, sisteminizde mevcut tüm kabuklar listelenecektir. Liste içinde, ön tanımlı olmasını istediğiniz kabuğun çalıştırılabilir dosya yolunu görüyorsanız sisteminizde yüklü demektir. Eğer görünmüyorsa gerekli kabuğu sisteminize yüklemeniz gerekiyor.

```sh
$ cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/bin/dash
/bin/bash
/bin/rbash
/usr/bin/screen
/usr/bin/fish

```

### `whereis` Komutu

Tanımlamak istediğimiz kabuğun dizin yolu `/etc/shells` dosyasında kayıtlı olmasına rağmen, sistemimizden kaldırılmış olabilir. İşaret edilen dizinde yüklü olduğundan emin olmak için, `whereis` komutunu kullanabilirsiniz. Bu komut belirtilen bir programın çalıştırılabilir dosyasının, kaynak ve manual dosyalarının bulunduğu konumları görüntüler.

```sh
$ whereis bash
bash: /bin/bash /etc/bash.bashrc /usr/share/man/man1/bash.1.gz
```

İlk sırada görülen `/bin/bash`, bash kabuğunun çalıştırılabilir dosyasının dosya yolunu gösteriyor. Bu bilgiye ihtiyacımız olacak çünkü ön tanımlı kabuk bilgisini girerken kabuğun tam yolunu girmemiz gerekecek. Bu yolu bir yere not edelim.

## ÖN TANIMLI KABUĞU DEĞİŞTİRME

Yeni kabuğun yerini belirledikten sonra, root veya süper kullanıcı şifresine sahip olduğunuz sürece herhangi bir kullanıcı için ön tanımlı kabuğu değiştirebilirsiniz.

Kullanıcıların ön tanımlı kabuk bilgileri `/etc/passwd` dosyasında tutulur. Burada bulunan bilgiyi el yordamıyla değiştirebileceğiniz gibi, `usermod` veya `chsh` komutlarını kullanarak da bu işlemi yapabilirsiniz.

### 1. 'chsh' Komutunu Kullanarak Değiştirme

`chsh` herhangi bir kullanıcının giriş kabuğunu veya varsayılan komut kabuğunu değiştirir. `-s` veya `-shell` parametreleri ile kullanarak, kullanıcı adını belirterek ve yukarıdaki [**mevcut kabuğu öğrenme**](#mevcut-kabuğu-öğrenme) başlığında anlatıldığı şekilde elde edilen, kabuğun çalıştırılabilir dosya yolunu girerek kullanıcının ön tanımlı kabuğunu değiştirebilirsiniz. Her kullanıcı kendi şifresiyle, kendi giriş kabuğunu bu komut ile değiştirebilir. Yönetici haklarına sahipseniz, root da dahil olmak üzere diğer bütün kullanıcıların ön tanımlı kabuklarını değiştirebilirsiniz. Kullanıcı adımızın `firat` olduğunu ve ön tanımlı kabuğu `bash` olarak değiştirmek istediğimizi düşünelim. Bu durumda komutu aşağıdaki gibi kullanabiliriz.

```sh
chsh -s /bin/bash firat
```

**Not:** Komutu çalıştırdıktan sonra `$ grep 'firat' /etc/passwd` komutu ile, kullanıcının kabuk programı değişikliğini gözlemleyebilirsiniz. Yaptığınız değişikliğin etkinleşmesi için, kullanıcı oturumunu yenilemeniz gerekiyor.
{: .notice--info}

### 2. 'usermod' Komutu ile Değiştirme

`usermod`, kullanıcı hesaplarını değiştirmek için kullanılan bir komuttur. `-s` veya `-shell` seçenekleri ile kullanarak ve kullanıcı adını belirterek, kullanıcının ön tanımlı kabuğunu değiştirebilirsiniz. `usermod` komutunu kullanabilmek için süper kullanıcı olmanız veya yönetici haklarına sahip olmanız gerekir. Eğer süper kullanıcı değilseniz komutu `sudo` komutu ile beraber kullanın. `firat` kullanıcısının ön tanımlı kabuğunu bash olarak belirlemek istiyorsak, komutu aşağıdaki gibi kullanabiliriz.

```sh
sudo usermod -s /bin/bash firat
```

**Not:** Komutu çalıştırdıktan sonra `$ grep 'firat' /etc/passwd` komutu ile, kullanıcının kabuk programı değişikliğini gözlemleyebilirsiniz. Yaptığınız değişikliğin etkinleşmesi için, kullanıcı oturumunu yenilemeniz gerekiyor.
{: .notice--info}

### 3. '/etc/passwd' Dosyasını Elle Düzenleme

**Dikkat:** `/etc/passwd` dosyası yönetici haklarıyla düzenlenir. Bu işlem yukarıdaki komutları kullanmaktan daha risklidir. Yönetici hakları ile bu dosya düzenlenirken sadece mevcut kullanıcının değil, `root` da dahil diğer tüm kullanıcıların kimlik bilgileri değiştirilebilir. Kontrolsüz bir müdahale, problemlere yol açabilir.
{: .notice--danger}

Dosyanın içeriğini görmek için `cat` komutunu kullanabilirsiniz.

```sh
firat@q4os-desktop:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
_apt:x:104:65534::/nonexistent:/bin/false
messagebus:x:105:108::/var/run/dbus:/bin/false
pulse:x:106:111:PulseAudio daemon,,,:/var/run/pulse:/bin/false
firat:x:1000:1000:Firat S.,,,:/home/firat:/usr/bin/fish
usbmux:x:107:46:usbmux daemon,,,:/var/lib/usbmux:/bin/false
```

Görüldüğü gibi, firat kullanıcısının bulunduğu satırda en son sırada ön tanımlı kabuk uygulamasının dosya yolu bulunuyor. Herhangi bir metin editörüyle bu dosyayı açarak, kullanıcının bulunduğu satırdaki kabuk bilgisini değiştirdiğinizde ve sistemi yeniden başlattığınızda ön tanımlı kabuğu değiştirmiş olursunuz. `/etc/passwd` dosyası her kullanıcı tarafından görüntülenebilir ancak düzenlemek için yönetici hakları gerektirir. Bu nedenle dosyayı yönetici hakları ile açmamız gerekiyor. Bu işlemi `sudo` komutu ile yapabilirsiniz.

```sh
sudo gedit /etc/passwd
```

Bu komut ile `passwd` dosyası yönetici haklarıyla açılır ve bütün içeriğinin değiştirilip kaydedilmesine izin verilir. Kullanıcı adınızın bulunduğu satırın en sonunda yer alan kabuk dizin yolunu  değiştirmeniz gerekiyor. Tabi değiştirmek istediğiniz kabuğun yüklü olduğu dizini de biliyor olmalısınız. Bu bilgiyi, **öğrenme** başlığı altında anlatılan `cat /etc/shells` ve `whereis <kabukadı>` komutları sayesinde öğrenebilirsiniz.

Diyelim ki `firat` kullanıcısının giriş kabuğunu bash olarak değiştireceğiz. Yukarıda `firat` kullanıcısının yazılı olduğu satırın sonunda yer alan kabuk dosya yolunu, aşağıdaki gibi değiştirmemiz gerekiyor.

```sh
firat:x:1000:1000:Firat S.,,,:/home/firat:/bin/bash
```

Ve dosyayı kaydediyoruz.

**Not:** Yaptığınız değişikliğin etkinleşmesi için, kullanıcı oturumunu yenilemeniz gerekiyor.
{: .notice--info}

### 4. Yönetici Haklarınız Yoksa, Süper Kullanıcı Değilseniz ve Kök Erişiminiz Yoksa

Bu durumda, `/etc/passwd` dosyasına ulaşamaz ve değiştiremezsiniz. Sadece mevcut kabuğu yürütme seçeneğiniz vardır. Ancak komut satırına kabuk adını yazıp çalıştırarak, istediğiniz kabuğa geçiş yapabilirsiniz. Aşağıda bash kabuğu kullanan bir kullanıcı, sistemde yüklü olan fish kabuğunu kullanmak için `fish` komutunu veriyor. Fish kabuğu komutla birlikte karşılama mesajını vererek çalışmaya başlıyor ve sonraki satırda bizden komut bekliyor. Artık bu dakikadan sonra verilen komutlar, fish kabuğu tarafından işlenecek.

```sh
firat@ubuntu-f:~$ fish
Welcome to fish, the friendly interactive shell
firat@ubuntu-f ~>
```

Aynı komut satırında `exit` komutunu verdiğinizde ise, fish kabuğundan çıkarak tekrar eski kabuğa (bash) dönebilirsiniz.

```sh
firat@ubuntu-f ~> exit
firat@ubuntu-f:~$
```

İkinci ve biraz daha kalıcı bir yöntem olarak, kabuğun başlatma dosyalarına gerekli komutları ekleyerek, mevcut varsayılan kabuk başlatıldığında diğer kabuğun çalışması sağlanabilir. Ancak bu işlem **ön tanımlı kabuğu değiştirmez**, sadece kabuğu kullanmanızı sağlar.

İlk olarak [**mevcut kabuğu öğrenme**](#mevcut-kabuğu-öğrenme) başlığında anlatıldığı gibi, kullanmak istediğiniz kabuğun dosya yolunu bulmanız gerekiyor. Daha sonra da mevcut kabuğunuzun yapılandırma dosyalarını bulun.

{% capture notice-text %}
**Yapılandırma dosyaları hakkında:** Linux'ta çalışan bir kabuk, "çalışma şekline" göre farklı kategorilere ayrılır. Her şekilde de çalışan program aynı olmasına rağmen, çalışma şekli bakımından bir kategoriye dahil olur.

- Giriş kabuğu (login shell)
- Giriş kabuğu olmayan kabuk (no login shell)

Ayrıca kullanıcı ile etkileşimi yönünden de iki kategoriye ayrılır.

- Etkileşimli kabuk (interactive shell)
- Etkileşimsiz kabuk (non-interactive shell)

Kabuk programları başlatıldığında, dahil olduğu kategorilere göre yürüttüğü bazı yapılandırma dosyaları vardır. (Aşağıdaki bilgiler **bash** kabuğu için geçerlidir. Diğer kabukların kullandığı yapılandırma dosyalarını, [**bu sayfada**](https://www.wikiwand.com/en/Unix_shell) bulunan [**'Configuration files'**](https://www.wikiwand.com/en/Unix_shell#/Configuration%20files) başlığı altındaki tablodan inceleyebilirsiniz.)

**Giriş kabuğu (Login shell):**

- **Etkileşimli** veya **etkileşimsiz** olarak bir **bash** kabuğu çağırmak için `--login` seçeneği kullanılır. Böylesi durumlarda bash, sırasıyla bazı komut dosyalarını okunur. ilk olarak genel ayarlar için `/etc/profile` dosyası mevcutsa, bu dosyadaki komutları okuyarak yürütür. Daha sonra kullanıcıya özel ayarlar için sırasıyla, `~/.bash_profile`, `~/.bash_login`, `~/.profile` dosyalarını arar ve bulduğu ilk sıradaki dosyanın komutlarını okuyarak yürütür. Bu dosyalar, `~/bashrc` dosyasını dahili olarak çağırdığı için herhangi birinin okunması durumunda `~/bashrc` dosyası da okunmuş olur. `--noprofile` seçeneği kullanılarak, başlangıçtaki bu davranış etkisiz hale getirilebilir. Giriş kabuğundan çıkılması durumunda, eğer mevcutsa `~/.bash_logout` dosyasını okur ve içindeki komutları yürütür.

**Giriş kabuğu olmayan kabuk (No login shell):**

- **Etkileşimli** olarak başlatıldığında, `~/.bashrc` dosyası mevcutsa içindeki komutları okur ve yürütür.  (--norc seçeneği kullanılarak bu davranış etkisiz duruma getirilebilir). Eğer farklı bir dosyanın yürütülmesi isteniyorsa, `--rcfile <dosyaismi>` seçeneği ile  `~/.bashrc` dosyası yerine farklı bir dosyanın okunması ve komutlarının çalıştırılması sağlanabilir.

- **Etkileşimsiz** olarak başlatıldığında, `$BASH_ENV` değişkenini arar. Değişken mevcut ise, taşıdığı değeri bir dosya adı olarak arar. Eğer dosya adı mevcutsa içindeki komutları okur ve yürütür.

{% endcapture %}

<div class="notice"> {{ notice-text | markdownify }} </div>

Yapılandırma dosyaları içinden rc dosyalarını kullanmanız daha yerinde olacaktır. Çünkü dosyaları **etkileşimli**, **etkileşimsiz** ve **giriş olan**, **giriş olmayan** kabuklar tarafından okunur. Hedef kabuk programınıza bağlı olarak, **bash** kabuğu kullanacaksanız `~/.bashrc` dosyasını, **csh** kabuğunu kullanacaksanız `~/.cshrc` dosyasını kullanmanız gerekir. Diğer kabukların kullandığı yapılandırma dosyalarını, [**'bu tablodan'**](https://www.wikiwand.com/en/Unix_shell#/Configuration%20files) inceleyebilirsiniz.

Diyelim ki mevcut kabuğunuz bash ve siz fish kabuğunu başlatmak istiyorsunuz. O halde `~/.bashrc` dosyasını açarak aşağıdaki komutları eklemeniz gerekiyor.

```sh
SHELL=/usr/bin/fish
exec /bin/bash --login
```

Eğer mevcut kabuğunuz csh ve siz bash kabuğunu başlatmak istiyorsunuz. Bu durumda da `~/.cshrc` dosyasını açarak aşağıdaki kodları eklemeniz gerekecekti.

```sh
setenv SHELL /bin/bash
exec /bin/bash --login
```

Genellikle çoğu kabuk `~/.login` veya `~/.profile` dosyalarını okuduğu için, bu dosyaları da kullanabilirsiniz. Ancak içine ekleyeceğiniz komutlar, ön tanımlı kabuğunuzun anladığı dilde olmalı. Ayrıca yeni yürütülecek kabuk da, başlatıldığında kendi yapılandırma dosyalarını okuyacağı için, artık bu dosyaları da kişiselleştirmeniz ve yapılandırmanız gerekmektedir.

Düzenlediğiniz yapılandırma dosyalarını etkinleştirmek için tekrar yorumlamanız gerekir. Bu işlemi `source` komutuyla yapabilirsiniz.

```sh
source ~/.bashrc
```

**Bilgi:** Bir komut dosyası çalıştırıldığında, bir alt kabukta çalışır. İşlem tamamlandığında alt kabuk kapanır ve denetim orijinal kabuğa geri döner. Komut dosyasında ayarlanan herhangi bir şey alt kabuk için geçerli olacağından, kabuk kapatıldığında bu ayarlar kaybolur. Komut dosyasını bir alt kabukta değil de geçerli kabukta çalıştırmak için `source` komutu kullanılır. Bu komutla çalışırılan komut dosyasında yapılan ayarlar, geçerli kabuk için geçerli olacaktır.
{: .notice}

**Not:** Yapılandırma dosyalarında yaptığınız değişikliklerde varsayılan ayarlara dönmek isterseniz, genellikle `/etc/skeleton`, `/etc/skel/` ya da `/etc/default` dizinleri altında bulunan aynı isimdeki dosyaları kullanabilirsiniz. Bu dosyalar, giriş sırasında ayarlanan **standart** ortam değişkenlerini tanımlayan betiklerdir. Bu dosyaları, içeriği değiştirilmiş dosyalarla yer değiştirdiğinizde sisteminiz ilk kurulumundaki tanımlara sahip olacaktır.
{: .notice--success}

Kaynaklar:  
<http://www.belgeler.org/bashref/bashref_startup.files.html>  
<https://tr.opensuse.org/Bash>  
<http://yapbenzet.kocaeli.edu.tr/wp-content/uploads/LINUX_AG_YONETIMI_HAFTA_8.pdf>  
<http://www.belgeler.org/bashref/bashref_interactive.shells.html>  
{: .notice--primary}