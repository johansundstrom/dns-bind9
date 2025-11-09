# Setup bind9

## Bakgrund

DNS-tjänster kan uppfylla minst 1 av tre olika scenarier.

1. *Internet DNS* DNS tjänst på Internet (på WAN) som pekar ut din personliga server (på LAN). I texten benämnd som ```domain.se```. Exempelvis ```ftp.domain.se```.
2. Lokal DNS som upplöser namn till IP lokalt. Oftast används domännamnet ```home.local```, exempelvis ```nas.home.local```.
3. Split Horizon (eller *Split DNS*) Båda ovanstående scenarier samtidigt

## 1. Internet DNS

Denna tjänst ger möjlighet att från internet ange domänadresser som pekar in i ett lokal nät. Det kan t.ex. vara att nå en webbserver, en FTP eller någon annan lokal service. För att detta ska fungera krävs att ha en domänadress. 

Min approach är att registera en .se-adress. Den blir personlig och lätt att komma ihåg. Annat tips är också att hålla den så kort som möjligt kort vilket innebär effektiva adresser om man ska uppge dessa ofta. 

Sök därefter upp en fri tjänst på Internet som låter dig administrera DNS-poster själv. Ett sådant är *simply.com*. Om man bara är ute efter att hantera DNS på Internet så är *Simply.com* gratis och låter för tillfället att göra detta på livstid, utan att ens registera ditt kontokort. Anvisningar följer därför just *Simply.com*.



## Installera

* Förutsättningar: samtliga instruktioner gäller linux, främst Ubuntu. 
* Hårdvara: RPI (Debian), PC (Ubuntu Server)

```sudo apt install bind9```

## Översikt - konfigurationer

Alla konfigurationer ligger i...

```/etc/bind```

Kopplingarna framgår av bilden. Pilen pekar mot referens

![Alt text](bind-files.png "Konfigurerbara filer i BIND")

## named.conf

Detta är den primära konfigurationen för BIND. Behöver inte ändras.

```bash
include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
include "/etc/bind/named.conf.default-zones";
```

## named.conf.options

Allmänn konfigureringsfil som...

* port att lyssna på
* tillåtna uppslag
* DNSSEC
* cashe filväg
* etc...

Ett exempel

```bash
acl internal-network {
  192.168.1.0/24;
};

options {
    directory "/var/cache/bind";

    recursion yes;
    allow-query { any; };
    allow-recursion { internal-network; localhost; };
    allow-transfer { none; };

    forwarders {
        8.8.8.8; // Google DNS
        8.8.4.4;
    };


    dnssec-validation auto;
    listen-on { any; };
    listen-on-v6 { any; };
};
```

2 
