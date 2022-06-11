# ZTE-MF297D_Nordic1_B0X-WPA3

The really first wiki online for hacking the router firmware used by (Telia_ZTE_-_MF297D) delivered from ZTE. This repo will be updated very often from today's date (2022-06-11), I choose to post everything here instead of reporting cve's, as usual! I start today and we'll see how it goes, lets begin.! :)

Alright, I just got the router `2022-06-11` and I saw Daniel Wong is the only guy that reported a cryptographic issues vulnerability.

# Router Info

I didn't add any WAN cable yet, first thing i did was to unscrew the router of course. It is brand new really! :)

My current firmware version is: `MF297D_Nordic1_B05` - I saw there is a new version from 2 days ago cause the cve report, so we want to turn off the autoupgrade of course. They forgot to deny the access for this, use below command for turn off tr069:

# CVE's 

| Date | Current Version    | News                            | Affected Version                          |
| :----------------- | :----------------- | :-------------------------------- | :----------------------------------|  
| 2022-06-09 | `MF297D`            |  MF297D_Nordic1_B05    | MF297D_Nordic1_B06                               |

ZTE's MF297D product has cryptographic issues vulnerability. 
Due to the use of weak random values, the security of the device is reduced, and it may face the risk of attack.

https://www.cve.org/CVERecord?id=CVE-2022-23138

# wuseman version

Alright, first we want is to DISABLE the autoupgrade so we will not upgrade to B06, 
they forgot to disable our access to tr69 it seems so turn off auto upgrade ASAP:

### Login:

```
curl 'http://192.168.32.1/goform/goform_set_cmd_process'  \
    -X POST -H 'TeliaHacking/1.0' \
    -H 'Accept: application/json, text/javascript, */*; q=0.01' \
    -H 'Accept-Language: en-US,en;q=0.5' -H 'Accept-Encoding: gzip, deflate' \
    -H 'Referer: http://192.168.32.1/' -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
    -H 'X-Requested-With: XMLHttpRequest' -H 'Origin: http://192.168.32.1' -H 'Connection: keep-alive' --data-raw 'isTest=false&goformId=LOGIN&password=<ztoken>'
```

### Disable tr69

... Good start - Sorry Telia! :)

To avoid to upgrade to the newer version, turn off tr069 or edit the ACSURL (wich is hidden and disabled for us but they forgot to disable it)

```console
curl 'http://192.168.32.1/goform/goform_set_cmd_process' -X POST \ 
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:101.0) Gecko/20100101 Firefox/101.0'  \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: en-US,en;q=0.5' \
  -H 'Accept-Encoding: gzip, deflate' -H 'Referer: http://192.168.32.1/' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8'\
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Origin: http://192.168.32.1' \
  -H 'Connection: keep-alive' \
  -H 'Cookie: zwsd="c59b469d724f7919b7d35514184fdc0f"' \
  --data-raw 'isTest=false&goformId=setTR069Config&serverURL=https%3A%2F%2Frgw.teliacompany.com%3A7575%2FACS-server%2FACS&serverusername=&serveruserpassword=&tr069_CPEPortNo=7547&connrequestname=&connrequestpassword=&tr069_PeriodicInformEnable=0&tr069_PeriodicInformInterval=99500&tr069_CertEnable=1&AD=<token>'
```

### Ups :) 

![Screenshot_20220611_064821](https://user-images.githubusercontent.com/26827453/173183143-b235d9d4-04a5-49d5-833e-106580315ea9.png)

### Config file is encrypted and salted, see below:

![Screenshot_20220611_060750](https://user-images.githubusercontent.com/26827453/173183374-d60a47e0-f26b-466b-9038-85d1a8d962f5.png)

### Export config file: 



