# ZTE-MF297D_Nordic1_B0X-WPA3

The really first wiki online for hacking the router firmware used by (Telia_ZTE_-_MF297D) delivered from ZTE. This repo will be updated very often from today's date (2022-06-11), I choose to post everything here instead of reporting cve's, as usual! I start today and we'll see how it goes, lets begin.! :)

Alright, I just got the router `2022-06-11` and I saw Daniel Wong is the only guy that reported a cryptographic issues vulnerability so now we have to find a way to hurry to turn off the upgrade from 2022-06-09! No worries, I got you covered! I will add everything I find, I will rewrite this wiki when I  there is time:)

### Router Info

I didn't add any WAN cable yet, first thing i did was to unscrew the router of course. It is brand new really! :)

My current firmware version is: `MF297D_Nordic1_B05` - All Telia routers will be shipped with this firmware I guess.

### Telia Version

![Screenshot_20220611_123128](https://user-images.githubusercontent.com/26827453/173184189-ecc6ad08-7e0e-4a44-bfc9-d88ec4c8be3c.png)

### Getting Started

Alright, first we want is to DISABLE the autoupgrade so we will not upgrade from `MF297D_Nordic1_B05` -> `MF297D_Nordic1_B06`, 
they forgot to disable access to tr69 it seems so turn off auto upgrade ASAP! 

### Disable tr69

... Good start - Sorry Telia! :) To avoid to upgrade to the newer version, turn off tr069 or edit the ACSURL (wich is hidden and disabled for us but they forgot to disable it)

```console
curl 'http://192.168.32.1/goform/goform_set_cmd_process' -X POST \ 
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: en-US,en;q=0.5' \
  -H 'Accept-Encoding: gzip, deflate' -H 'Referer: http://192.168.32.1/' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8'\
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Cookie: zwsd="<zwsd:_cookie>"' \
  --data-raw 'isTest=false&goformId=setTR069Config&serverURL=wusemanwasherehttps%3A%2F%2Frgw.teliacompany.com%3A7575%2FACS-server%2FACS&serverusername=&serveruserpassword=&tr069_CPEPortNo=7547&connrequestname=&connrequestpassword=&tr069_PeriodicInformEnable=0&tr069_PeriodicInformInterval=99500&tr069_CertEnable=1&AD=<token>'
```

### Router Login:

```
curl 'http://192.168.32.1/goform/goform_set_cmd_process'  \
    -X POST -H 'TeliaHacking/1.0' \
    -H 'Accept: application/json, text/javascript, */*; q=0.01' \
    -H 'Accept-Language: en-US,en;q=0.5' -H 'Accept-Encoding: gzip, deflate' \
    -H 'Referer: http://192.168.32.1/' -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
    -H 'X-Requested-With: XMLHttpRequest' \
    -H 'Origin: http://192.168.32.1' \
    -H 'Connection: keep-alive' --data-raw 'isTest=false&goformId=LOGIN&password=<ztoken>'
```

### Get Telnet info: 

![telnet](https://user-images.githubusercontent.com/26827453/173183876-fe01e47d-7973-48bd-aeb1-fde07da2207d.png)


### Config file is encrypted and salted, see below:

![Screenshot_20220611_060724](https://user-images.githubusercontent.com/26827453/173183897-9c243d9e-25f8-4718-b4a7-8e6a7985e7c9.png)

# CVE reports by others

| Date | Current Version    | Affected Version                            |  Fixed Version                          |
| :----------------- | :----------------- | :-------------------------------- | :----------------------------------|  
| 2022-06-09 | `MF297D`            |  MF297D_Nordic1_B05    | MF297D_Nordic1_B06                               |

ZTE's MF297D product has cryptographic issues vulnerability. 
Due to the use of weak random values, the security of the device is reduced, and it may face the risk of attack. 

https://www.cve.org/CVERecord?id=CVE-2022-23138




