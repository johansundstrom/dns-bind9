# Setup bind9

## Bakgrund

DNS-tjänster kan uppfylla minst 1 av tre olika scenarier.

* Lokal DNS som upplöser namn till IP lokalt. Oftast används domännamnet ```home.local``` i dessa fall. T.ex. ```nas.home.local```.
* DNS tjänst på Internet (på WAN) som pekar ut din personliga server (på LAN). I texten benämnd som ```domain.se```. Exempelvis ```ftp.domain.se```
* Split Horizon (Split DNS) Båda ovanstående scenarier samtidigt

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
