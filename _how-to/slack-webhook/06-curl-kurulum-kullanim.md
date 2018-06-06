---
title: "CURL Kurulumu ve Kullanımı"
permalink: /how-to/slack-webhook/curl-kurulum-kullanim/
excerpt: "CURL aracının Linux ortamına kurulumu ve kullanımı"
last_modified_at: 2018-05-24T20:20:00-10:00
classes: wide
tags:
  - Slack
  - Webhook
  - CURL
  - Linux
---

Slack üzerinde yeni bir Incoming Webhooks entegrasyonu oluşturduktan sonra, bizden istenen `JSON` formatındaki yapıyı, verilen bağlantıya `POST` metoduyla göndermemiz gerekiyor. Bu işlemi yapabileceğimiz [HTTPie](https://httpie.org/), [HTTP Prompt](http://http-prompt.com/), [Wget](https://www.gnu.org/software/wget/) gibi çeşitli araçlar mevcut. Biz bu işlemi [CURL](https://curl.haxx.se/) aracıyla, Linux komut satırı üzerinden yapacağız. 

**CURL:** Bu araç sayesinde bir sunucuya (ya da sunucudan) `HTTPS` istekleri yapabiliyor, elimizdeki verileri aktarabiliyoruz. Kendi klavuz sayfasında belirtildiğine göre `HTTPS` yanında `DICT`, `FILE`, `FTP`, `FTPS`, `GOPHER`, `HTTP`, `IMAP`, `IMAPS`, `LDAP`, `LDAPS`, `POP3`, `POP3S`, `RTMP`, `RTSP`, `SCP`, `SFTP`, `SMB`, `SMBS`, `SMTP`, `SMTPS`, `TELNET` ve `TFTP` standartlarını da destekliyor.
{: .notice--info}

### CURL Kurulumu

Bazı Linux sürümleriyle beraber gelen bu scriptin sisteminizde yüklü olup olmadığını anlamak için şu komutu kullanın.

```bash
$ curl -V
```

Eğer bu komuttan sonra aşağıdaki gibi bir versiyon bilgisi dönüyorsa Curl aracı sisteminizde kurulu demektir.

```bash
curl 7.55.1 (x86_64-pc-linux-gnu) libcurl/7.55.1 OpenSSL/1.0.2g zlib/1.2.11 libidn2/2.0.2 libpsl/0.18.0 (+libidn2/2.0.2) librtmp/2.3
Release-Date: 2017-08-14
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtmp rtsp smb smbs smtp smtps telnet tftp 
Features: AsynchDNS IDN IPv6 Largefile GSS-API Kerberos SPNEGO NTLM NTLM_WB SSL libz TLS-SRP UnixSockets HTTPS-proxy PSL 
```

Versiyon bilgisi yerine `komut bulunamadı` uyarısı ekranda görünüyorsa, Debian tabanlı sistemler için alttaki komutla sisteminize kurabilirsiniz.

```bash
$ apt-get install curl
```

Redhat tabanlı sistemler için de alttaki komutu kullanabilirsiniz.

```bash
$ yum install curl
```

**Not:** Diğer platformlar için yükleme paketlerine [bu adresten](https://curl.haxx.se/download.html) ulaşabilirsiniz.
{: .notice--info}

Kurulum tamamlandığında, yukarıdaki komutu tekrar çalıştırdığınızda artık Curl versiyon bilgisini görebilmelisiniz.

```bash
$ curl -V

curl 7.55.1 (x86_64-pc-linux-gnu) libcurl/7.55.1 OpenSSL/1.0.2g zlib/1.2.11 libidn2/2.0.2 libpsl/0.18.0 (+libidn2/2.0.2) librtmp/2.3
Release-Date: 2017-08-14
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtmp rtsp smb smbs smtp smtps telnet tftp 
Features: AsynchDNS IDN IPv6 Largefile GSS-API Kerberos SPNEGO NTLM NTLM_WB SSL libz TLS-SRP UnixSockets HTTPS-proxy PSL 
```

### CURL Kullanımı

Bu aşamadan itibaren artık Curl kullanmaya başlayabilirz. Hızlı bir deneme olarak, komut satırına aşağıdaki kodu yazalım.

```bash
$ curl wttr.in/izmir
```

Terminal ekranınızda yukarıdaki gibi, [wttr.in](http://wttr.in/) sitesinden alınan İzmir'e ait hava durumu bilgileri görünmeli.

```bash
firat@ubuntu-f:~$ curl http://wttr.in/izmir
Weather report: Izmir, Turkey

    \  /       Partly cloudy
  _ /"".-.     22 °C          
    \_(   ).   ↗ 2 km/h       
    /(___(__)  20 km          
               0.0 mm         
                                                       ┌─────────────┐                                                       
┌──────────────────────────────┬───────────────────────┤  Thu 24 May ├───────────────────────┬──────────────────────────────┐
│            Morning           │             Noon      └──────┬──────┘     Evening           │             Night            │
├──────────────────────────────┼──────────────────────────────┼──────────────────────────────┼──────────────────────────────┤
│    \  /       Partly cloudy  │    \  /       Partly cloudy  │    \  /       Partly cloudy  │  _`/"".-.     Patchy rain po…│
│  _ /"".-.     26 °C          │  _ /"".-.     28 °C          │  _ /"".-.     25-27 °C       │   ,\_(   ).   22-24 °C       │
│    \_(   ).   → 5 km/h       │    \_(   ).   → 12-13 km/h   │    \_(   ).   → 11-15 km/h   │    /(___(__)  → 6-10 km/h    │
│    /(___(__)  20 km          │    /(___(__)  20 km          │    /(___(__)  20 km          │      ‘ ‘ ‘ ‘  19 km          │
│               0.0 mm | 0%    │               0.0 mm | 0%    │               0.0 mm | 0%    │     ‘ ‘ ‘ ‘   0.0 mm | 21%   │
└──────────────────────────────┴──────────────────────────────┴──────────────────────────────┴──────────────────────────────┘
                                                       ┌─────────────┐                                                       
┌──────────────────────────────┬───────────────────────┤  Fri 25 May ├───────────────────────┬──────────────────────────────┐
│            Morning           │             Noon      └──────┬──────┘     Evening           │             Night            │
├──────────────────────────────┼──────────────────────────────┼──────────────────────────────┼──────────────────────────────┤
│    \  /       Partly cloudy  │    \  /       Partly cloudy  │    \  /       Partly cloudy  │    \  /       Partly cloudy  │
│  _ /"".-.     24-25 °C       │  _ /"".-.     27 °C          │  _ /"".-.     27 °C          │  _ /"".-.     23-25 °C       │
│    \_(   ).   ↓ 16-19 km/h   │    \_(   ).   ↓ 23-26 km/h   │    \_(   ).   ↓ 25-31 km/h   │    \_(   ).   ↖ 18-27 km/h   │
│    /(___(__)  19 km          │    /(___(__)  19 km          │    /(___(__)  19 km          │    /(___(__)  19 km          │
│               0.0 mm | 0%    │               0.0 mm | 0%    │               0.0 mm | 0%    │               0.0 mm | 0%    │
└──────────────────────────────┴──────────────────────────────┴──────────────────────────────┴──────────────────────────────┘
                                                       ┌─────────────┐                                                       
┌──────────────────────────────┬───────────────────────┤  Sat 26 May ├───────────────────────┬──────────────────────────────┐
│            Morning           │             Noon      └──────┬──────┘     Evening           │             Night            │
├──────────────────────────────┼──────────────────────────────┼──────────────────────────────┼──────────────────────────────┤
│  _`/"".-.     Thundery outbr…│  _`/"".-.     Thundery outbr…│  _`/"".-.     Patchy rain po…│    \  /       Partly cloudy  │
│   ,\_(   ).   24-26 °C       │   ,\_(   ).   27 °C          │   ,\_(   ).   26-27 °C       │  _ /"".-.     23-25 °C       │
│    /(___(__)  ↓ 20-23 km/h   │    /(___(__)  ↓ 25-29 km/h   │    /(___(__)  ↓ 23-31 km/h   │    \_(   ).   ↖ 15-22 km/h   │
│     ⚡‘ ‘⚡‘ ‘  19 km          │     ⚡‘ ‘⚡‘ ‘  19 km          │      ‘ ‘ ‘ ‘  18 km          │    /(___(__)  18 km          │
│     ‘ ‘ ‘ ‘   0.0 mm | 0%    │     ‘ ‘ ‘ ‘   0.0 mm | 0%    │     ‘ ‘ ‘ ‘   0.6 mm | 78%   │               0.3 mm | 42%   │
└──────────────────────────────┴──────────────────────────────┴──────────────────────────────┴──────────────────────────────┘

New feature: multilingual location names wttr.in/станция+Восток (in UTF-8) and location search wttr.in/~Kilimanjaro (just add ~ before)
Follow @igor_chubin for wttr.in updates
```

İşte sonraki adımlarda biz de, hazırladığımız mesaj yapısını ve bazı gerekli parametreleri, CURL aracını kullanarak, Slack'den aldığımız Webhook URL'ine göndereceğiz. 


