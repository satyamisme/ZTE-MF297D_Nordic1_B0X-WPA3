# ZTE-MF297D_Nordic1_B0X-WPA3

The really first wiki online for hacking the router firmware used by (Telia_ZTE_-_MF297D) delivered from ZTE. This repo will be updated very often from today's date (2022-06-11), I choose to post everything here instead of reporting cve's, as usual! I start today and we'll see how it goes, lets begin.! :)

Alright, I just got the router `2022-06-11` and I saw Daniel Wong is the only guy that reported a cryptographic issues vulnerability so now we have to find a way to hurry to turn off the upgrade from 2022-06-09! No worries, I got you covered! I will add everything I find, I will rewrite this wiki when I  there is time:)

### Router Info

I didn't add any WAN cable yet, first thing i did was to unscrew the router of course. It is brand new really! :)

My current firmware version is: `MF297D_Nordic1_B05` - All Telia routers will be shipped with this firmware I guess.

### Telia Version

![Screenshot_m20220611_123128](https://user-images.githubusercontent.com/26827453/173184189-ecc6ad08-7e0e-4a44-bfc9-d88ec4c8be3c.png)

### Getting Started

Alright, first we want is to DISABLE the autoupgrade so we will not upgrade from `MF297D_Nordic1_B05` -> `MF297D_Nordic1_B06`, 
they forgot to disable access to tr69 it seems so turn off auto upgrade ASAP! 


###### WebToken

We need to add an additional requirement more when we decide to communicate with the “/goform/goform_set_cmd_process” endpoint (Figure 24).
There is a function called at `AD_hash_check_and_allowedlist`

Depending on how strong the randomness of the `RD` value is, we could try to brute-force it, but we won’t focus on that here. Using pre-auth `XSS`, we can obtain RD value sending the request in Figure 27 and calculate the `AD` value. This function will return true also when the “goformId” value is one of the entries in the `g_set_commands_v`” array. If we choose one of these `ID`s, we don't need to care about the `AD` parameter in our request. After examining all related handlers for the above form `ID`s, something interesting stood out.



### Developer Console (Temp Not Real Commands For TOKEN)
![Screenshot_20220821_003552](https://user-images.githubusercontent.com/26827453/185768301-014e55c1-cb8c-4368-bad6-bcef9243451c.png)

* Example usage, to be updated

```json
rd0
'BD_TELIASEMF297DMODV1.0.0B03 [Jan  5 2022 18:02:19]'
```

```json
rd1
'MF297D_Nordic1_B05'
```

```json
var AD_test=rd1+rd0
```

```json
SHA256('AD_test')
'C7BF4BBDBCD88D9D7F7C7B299C94E9E52091AF2FD2888ECF85A9D6A4160B4184'
```
```json
b64_md5('AD_test')
'4YLrvBZtczZueYaBOn/F8Q'
```

### Login on router, no other methods works and the ones around the web is old, do as below for login:

##### Get Password for using instead of AD WebToken for Curl :)

```bash
admin_Password=$(curl -sL 'http://192.168.32.1/goform/goform_get_cmd_process?isTest=false&cmd=admin_Password' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: en-US,en;q=0.9,sv;q=0.8' \
  -H 'Connection: keep-alive' \
  -H 'Cookie: zwsd="9db95bed7fecf8c4305252a4f6e12411"' \
  -H 'Referer: http://192.168.32.1/' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --compressed \
  --insecure|jq)
  
   echo $admin_Password|cut -d'"' -f4
```

##### Simple Login Via Password

![admin_password](https://user-images.githubusercontent.com/26827453/185767478-f6b3bc48-cdc5-4178-b306-c7d458b6cb79.png)

```bash
curl -sL 'http://192.168.32.1/goform/goform_set_cmd_process'   \
      -H 'Accept: application/json, text/javascript, */*; q=0.01'   \
      -H 'Accept-Language: en-US,en;q=0.9,sv;q=0.8' \
      -H 'Connection: keep-alive'     \
      -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8'    \
      -H 'Origin: http://192.168.32.1'   \
      -H 'Referer: http://192.168.32.1/'    \
      -H 'Cookie: zwsd="8bb88f80d334b1869781beb89f7b73be' \
      -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36'   \
      -H 'X-Requested-With: XMLHttpRequest'    \
      --data-raw 'isTest=false&goformId=LOGIN&password=506F30DEDC18694E4F2A347033BD5FC1D07389C7CB7970BA804FC4EB3DF604E8'|jq
```

### Set WebLanguage via Password instead of WebToken

![success](https://user-images.githubusercontent.com/26827453/185767477-2160419c-62fa-41f2-bb43-653b5c4b1dae.png)

```bash
curl -sL 'http://192.168.32.1/goform/goform_set_cmd_process'   \
      -H 'Accept: application/json, text/javascript, */*; q=0.01'   \
      -H 'Accept-Language: en-US,en;q=0.9,sv;q=0.8' \
      -H 'Connection: keep-alive'     \
      -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8'    \
      -H 'Origin: http://192.168.32.1'   \
      -H 'Referer: http://192.168.32.1/'    \
      -H 'Cookie: zwsd="8bb88f80d334b1869781beb89f7b73be' \
      -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36'   \
      -H 'X-Requested-With: XMLHttpRequest'    \
      --data-raw 'isTest=false&goformId=SET_WEB_LANGUAGE&Language=sv&password=506F30DEDC18694E4F2A347033BD5FC1D07389C7CB7970BA804FC4EB3DF604E8'|jq
```

##### Generate RD Token

```bash
RD=$(curl 'http://192.168.32.1/goform/goform_get_cmd_process?isTest=false&cmd=RD&_=1660960608154' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: en-US,en;q=0.9,sv;q=0.8' \
  -H 'Connection: keep-alive' \
  -H 'Cookie: zwsd="8bb88f80d334b1869781beb89f7b73be"' \
  -H 'Referer: http://192.168.32.1/' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --compressed \
  --insecure|cut -d'"' -f4)

echo ${RD}
```

##### Generate LD Token

```bash
LD="$(curl 'http://192.168.32.1/goform/goform_get_cmd_process?isTest=false&cmd=LD&_=1655363462531' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: sv-SE,sv;q=0.9,en-US;q=0.8,en;q=0.7' \
  -H 'Connection: keep-alive' \
  -H 'Referer: http://192.168.32.1/' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --compressed \
  --insecure|cut -d'"' -f4)"
  
echo ${LD}
```

### Disable TR-069 via commandline

To avoid to upgrade to the newer version, turn off tr069 or edit the ACSURL (wich is hidden and disabled for us but they forgot to disable it)

```bash
curl 'http://192.168.32.1/goform/goform_set_cmd_process' -X POST \ 
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: en-US,en;q=0.5' \
  -H 'Accept-Encoding: gzip, deflate' -H 'Referer: http://192.168.32.1/' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8'\
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Cookie: zwsd="<zwsd:_cookie>"' \
  --data-raw 'isTest=false&goformId=setTR069Config&serverURL=wusemanwasherehttps%3A%2F%2Frgw.teliacompany.com%3A7575%2FACS-server%2FACS&serverusername=&serveruserpassword=&tr069_CPEPortNo=7547&connrequestname=&connrequestpassword=&tr069_PeriodicInformEnable=0&tr069_PeriodicInformInterval=99500&tr069_CertEnable=1&AD=<token>'
```

### Print Access Control ACL Mode (Default: 2)

```bash
curl 'http://192.168.32.1/goform/goform_get_cmd_process?cmd=queryDeviceAccessControlList' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: en-US,en;q=0.9,sv;q=0.8' \
  -H 'Connection: keep-alive' \
  -H 'Cookie: zwsd="9db95bed7fecf8c4305252a4f6e12411"' \
  -H 'Referer: http://192.168.32.1/' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --compressed \
  --insecure
```

### Print Stuff for AD Token 

```bash
curl 'http://192.168.32.1/goform/goform_get_cmd_process?cmd=IOT_product_type%2Cloginfo%2Cmanufacturer%2Cmodel_name%2Cproduct_type%2Cwa_inner_version%2Cweb_api_version%2Cwifi_nv_api_version&multi_data=1' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: en-US,en;q=0.9,sv;q=0.8' \
  -H 'Connection: keep-alive' \
  -H 'Cookie: zwsd="9db95bed7fecf8c4305252a4f6e12411"' \
  -H 'Referer: http://192.168.32.1/' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --compressed \
  --insecure
```
```
{
  "IOT_product_type": "",
  "loginfo": "ok",
  "manufacturer": "",
  "model_name": "MF297D",
  "product_type": "CPE",
  "wa_inner_version": "BD_TELIASEMF297DMODV1.0.0B03 [Jan  5 2022 18:02:19]",
  "web_api_version": "",
  "wifi_nv_api_version": "2"
}
```



### Get Telnet info: 

![telnet](https://user-images.githubusercontent.com/26827453/173183876-fe01e47d-7973-48bd-aeb1-fde07da2207d.png)

### Config file is encrypted and salted, see below:

![Screenshot_20220611_060724](https://user-images.githubusercontent.com/26827453/173183897-9c243d9e-25f8-4718-b4a7-8e6a7985e7c9.png)

### CVE reports by others

| Date | Current Version    | Affected Version                            |  Fixed Version                          |
| :----------------- | :----------------- | :-------------------------------- | :----------------------------------|  
| 2022-06-09 | `MF297D`            |  MF297D_Nordic1_B05    | MF297D_Nordic1_B06                               |

ZTE's MF297D product has cryptographic issues vulnerability. 
Due to the use of weak random values, the security of the device is reduced, and it may face the risk of attack. 

https://www.cve.org/CVERecord?id=CVE-2022-23138

### Available WebUI Pages

```bash
cat menu4g.js \
  |grep -i hash\
  |sed 's/: "/http:\/\/192.168.32.1\/index.html/g'\
  |grep -o 'http.*"'\
  |cut -d'"' -f1
```

```bash
curl -sL http://192.168.32.1/config.json
```

```json
{
  "IS_TEST": false,
  "IS_SUPPORT_NEW_ZTELINK": true,
  "IS_SUPPORT_REMOTE": false,
  "DEVICE_MODEL": "MF297D",
  "REMOTE_MANAGEMENT_SUPPORT": false,
  "LIGHT_SUPPORT": true,
  "BAND_STEERING_SWITCH": true,
  "SUGGESTED_POSITION_SUPPORT": false,
  "PARENTAL_CONTROL_NEW_INTERFACE_SUPPORT": true,
  "WIFI_MESH_SUPPORT": false,
  "MAIN_ROLE": "6",
  "SUB_ROLE": "7",
  "WIFI_GUEST_SSID_ACCESS_SUPPORT": true,
  "WIFI_MAC_FILTER_SUPPORT": true,
  "HAS_GDPR": true,
  "HAS_USER_IMPROVEMENT": false,
  "PRODUCT_TYPE": "CPE",
  "HAS_LOGIN": true,
  "LOGIN_THEN_CHECK_PIN": true,
  "defaultRoute": "#login",
  "LOGIN_SECURITY_SUPPORT": true,
  "MAX_LOGIN_COUNT": 5,
  "GUEST_HASH": [
    "#httpshare_guest"
  ],
  "INCLUDE_MOBILE": true,
  "DEVICE": "cpe/MF297D",
  "PASSWORD_ENCODE": true,
  "EMPTY_APN_SUPPORT": false,
  "FAST_BOOT_SUPPORT": true,
  "HAS_STK": false,
  "HAS_CASCADE_SMS": true,
  "HAS_FOTA": true,
  "HAS_MULTI_SSID": true,
  "HAS_WIFI": true,
  "HAS_BATTERY": false,
  "SHOW_MAC_ADDRESS": true,
  "IPV6_SUPPORT": true,
  "IPV4V6_SUPPORT": true,
  "IPV4_AND_V6_SUPPORT": false,
  "CLEAR_DATA_SUPPORT": false,
  "USE_IPV6_INTERFACE": true,
  "MAX_STATION_NUMBER": 64,
  "MAX_STATION_NUMBER_5G": 64,
  "NETWORK_UNLOCK_SUPPORT": false,
  "WIFI_BAND_SUPPORT": false,
  "WIFI_BANDWIDTH_SUPPORT": true,
  "WIFI_SUPPORT_QR_CODE": true,
  "WIFI_SWITCH_SUPPORT": true,
  "WIFI_SLEEP_SUPPORT": false,
  "AUTO_POWER_SAVE_SUPPORT": false,
  "SHOW_WIFI_AP_ISOLATED": true,
  "STATION_BLOCK_SUPPORT": true,
  "STATION_BIND_SUPPORT": false,
  "UPGRADE_TYPE": "FOTA",
  "ALREADY_NOTICE": false,
  "HAS_OTA_NEW_VERSION": false,
  "ALREADY_OTA_NOTICE": false,
  "AP_STATION_SUPPORT": false,
  "AP_STATION_LIST_LENGTH": 10,
  "TSW_SUPPORT": true,
  "HAS_PHONEBOOK": true,
  "HAS_SMS": true,
  "SMS_DATABASE_SORT_SUPPORT": true,
  "SHOW_UN_COMPLETE_CONCAT_SMS": true,
  "SMS_UNREAD_NUM_INCLUDE_SIM": false,
  "SMS_SET_READ_WHEN_COMPLETE": false,
  "DATE_FORMAT": "3",
  "TIME_FORMAT": "24",
  "SD_CARD_SUPPORT": false,
  "VOLTE_SUPPORT": true,
  "WEBUI_TITLE": "4G+ Broadband MF297D",
  "TEMPORARY_MODEM_MAIN_STATE": [
    "modem_undetected",
    "modem_detected",
    "modem_sim_state",
    "modem_handover",
    "modem_imsi_lock",
    "modem_online",
    "modem_offline"
  ],
  "SHOW_APN_DNS": false,
  "HAS_PARENTAL_CONTROL": true,
  "WIFI_HAS_5G": true,
  "AC_MODE_SUPPORT": true,
  "OPMODE_CHANGE_SUPPORT": true,
  "OPERATE_MODE": [
    "PPP",
    "PPPOE",
    "AUTO",
    "LTE_BRIDGE"
  ],
  "isP82m": true,
  "MESH_STATE_ROUTER": "2",
  "MESH_STATE_CAP": "6",
  "MESH_STATE_RE": "1",
  "MESH_STATE_PRECAP": "3",
  "MESH_STATE_PRERE": "4",
  "WIFI_BEAMFORMING_SUPPORT": true,
  "homeRoute": "home",
  "defaultRouteM": "login",
  "isLogin": false,
  "isTrafficAlertSet": false,
  "DEVICE_MODE": "",
  "DEVICE_TYPE": "CPE",
  "SIM_CARD_STATUS": "",
  "DEVICE_NAME": "",
  "HAS_SAVE_LOGIN_PASSWORD": false,
  "HAS_TR069": false,
  "PASSWORD_ENCODE_SHA256": true,
  "WEB_ATTR_IF_SUPPORT_SHA256": 2,
  "ACCESSIBLE_ID_SUPPORT": true,
  "IS_SHOW_SINR": false,
  "IS_SUPORT_NIGHT_MODE": true,
  "IS_SUPORT_USER_IMPROVMENT": false,
  "WIFI_DOUBLE_CHIP": true,
  "WIFI_POINT_INFO": {},
  "WIFI_CHIP_CAPACITY": {},
  "ACCESSPOINTINDEX": {
    "FIRST": 0,
    "SECOND": 1,
    "THIRD": 2,
    "FOURTH": 3
  },
  "WIFICHIPINDEX": {
    "FIRST": 0,
    "SECOND": 1
  },
  "IS_SUPPORT_MULIT_USER": false,
  "IS_SUPPORT_NEW_TIMED_RESTART": true,
  "CONTENT_MODIFIED": {
    "modified": false,
    "message": "leave_page_info",
    "data": {},
    "callback": {}
  },
  "portForwardMax": 10,
  "urlFilterMax": 10,
  "defaultApnSize": 0,
  "maxApnNumber": 10,
  "staticMacIPAddress": 10,
  "NETWORK_MODES": [
    {
      "name": "802.11 b/g/n",
      "value": "4"
    },
    {
      "name": "11axg",
      "value": "7"
    }
  ],
  "NETWORK_MODES_BAND": [
    {
      "name": "802.11 a only",
      "value": "5"
    },
    {
      "name": "802.11 n only",
      "value": "2"
    },
    {
      "name": "802.11 a/n",
      "value": "4"
    },
    {
      "name": "11a/11n/11ac",
      "value": "6"
    },
    {
      "name": "11axa",
      "value": "8"
    }
  ],
  "NETWORK_MODES_AC": [
    {
      "name": "802.11 a/n/ac)",
      "value": "6"
    },
    {
      "name": "802.11 a/n/ac/ax",
      "value": "8"
    }
  ],
  "KEY_ID_MODES": [
    {
      "name": "Key 1",
      "value": "0"
    },
    {
      "name": "Key 2",
      "value": "1"
    },
    {
      "name": "Key 3",
      "value": "2"
    },
    {
      "name": "Key 4",
      "value": "3"
    }
  ],
  "ENCRYPT_TYPE_MODES": [
    {
      "name": "NO ENCRYPTION",
      "value": "NONE"
    },
    {
      "name": "WEP",
      "value": "WEP"
    }
  ],
  "AUTH_MODES": [
    {
      "name": "NO ENCRYPTION",
      "value": "OPEN"
    },
    {
      "name": "WPA2(AES)-PSK",
      "value": "WPA2PSK"
    },
    {
      "name": "WPA-PSK/WPA2-PSK",
      "value": "WPAPSKWPA2PSK"
    },
    {
      "name": "WPA-PSK/WPA2-PSK",
      "value": "WPA2PSKWPA3PSK"
    }
  ],
  "AUTH_MODES_ALL": [
    {
      "name": "NO ENCRYPTION",
      "value": "OPEN"
    },
    {
      "name": "SHARED",
      "value": "SHARED"
    },
    {
      "name": "WPA-PSK",
      "value": "WPAPSK"
    },
    {
      "name": "WPA2-PSK",
      "value": "WPA2PSK"
    },
    {
      "name": "WPA-PSK/WPA2-PSK",
      "value": "WPAPSKWPA2PSK"
    }
  ],
  "LANGUAGES": [
    {
      "name": "English",
      "value": "en"
    },
    {
      "name": "Svenska",
      "value": "sv"
    },
    {
      "name": "Dansk",
      "value": "da"
    },
    {
      "name": "Norsk",
      "value": "no"
    },
    {
      "name": "Suomi",
      "value": "fi"
    },
    {
      "name": "Lietuvių",
      "value": "lt"
    }
  ],
  "AUTO_MODES": [
    {
      "name": "Automatic",
      "value": "NETWORK_auto"
    },
    {
      "name": "4G Only",
      "value": "Only_LTE"
    }
  ],
  "APN_AUTH_MODES": [
    {
      "name": "NONE",
      "value": "none"
    },
    {
      "name": "CHAP",
      "value": "chap"
    },
    {
      "name": "PAP",
      "value": "pap"
    }
  ],
  "SMS_VALIDITY": [
    {
      "name": "12 hours",
      "value": "twelve_hours"
    },
    {
      "name": "A day",
      "value": "one_day"
    },
    {
      "name": "A week",
      "value": "one_week"
    },
    {
      "name": "The longest period",
      "value": "largest"
    }
  ],
  "SLEEP_MODES": [
    {
      "name": "Always on",
      "value": "-1"
    },
    {
      "name": "5 minutes",
      "value": "5"
    },
    {
      "name": "10 minutes",
      "value": "10"
    },
    {
      "name": "20 minutes",
      "value": "20"
    },
    {
      "name": "30 minutes",
      "value": "30"
    },
    {
      "name": "1 hour",
      "value": "60"
    },
    {
      "name": "2 hours",
      "value": "120"
    }
  ],
  "FORWARD_PROTOCOL_MODES": [
    {
      "name": "TCP+UDP",
      "value": "TCP&UDP"
    },
    {
      "name": "TCP",
      "value": "TCP"
    },
    {
      "name": "UDP",
      "value": "UDP"
    }
  ],
  "MAP_PROTOCOL_MODES": [
    {
      "name": "TCP+UDP",
      "value": "TCP&UDP"
    },
    {
      "name": "TCP",
      "value": "TCP"
    },
    {
      "name": "UDP",
      "value": "UDP"
    }
  ],
  "FILTER_PROTOCOL_MODES": [
    {
      "name": "NONE",
      "value": "None"
    },
    {
      "name": "TCP",
      "value": "TCP"
    },
    {
      "name": "UDP",
      "value": "UDP"
    },
    {
      "name": "ICMP",
      "value": "ICMP"
    }
  ],
  "SD_SHARE_ENABLE": [
    {
      "name": "Enable",
      "value": "1"
    },
    {
      "name": "Disable",
      "value": "0"
    }
  ],
  "SD_FILE_TO_SHARE": [
    {
      "name": "entire_sd_card",
      "value": "1"
    },
    {
      "name": "custom_setting",
      "value": "0"
    }
  ],
  "SD_ACCESS_TYPE": [
    {
      "name": "entire_sd_card",
      "value": "1"
    },
    {
      "name": "custom_setting",
      "value": "0"
    }
  ],
  "DLNA_LANGUAGES": [
    {
      "name": "english",
      "value": "english"
    },
    {
      "name": "chinese",
      "value": "chinese"
    }
  ],
  "SD_BASE_PATH": "/mmc2",
  "dbMsgs": [],
  "listMsgs": [],
  "currentChatObject": null,
  "smsMaxId": 0,
  "phonebook": [],
  "smsIsReady": false,
  "countryCodeType": {
    "world": 3,
    "mkkc": 3,
    "apld": 7,
    "etsic": 3,
    "fcca": 1
  },
  "countryCode": {
    "world": [
      "AL",
      "DZ",
      "AR",
      "AM",
      "AU",
      "AT",
      "AZ",
      "BH",
      "BY",
      "BE",
      "BA",
      "BR",
      "BN",
      "BG",
      "CL",
      "CN",
      "CR",
      "HR",
      "CY",
      "CZ",
      "DK",
      "EC",
      "EG",
      "SV",
      "EE",
      "FI",
      "FR",
      "FO",
      "GE",
      "DE",
      "GR",
      "HN",
      "HK",
      "HU",
      "IS",
      "IN",
      "ID",
      "IE",
      "IL",
      "IT",
      "JM",
      "JO",
      "KZ",
      "KE",
      "KW",
      "LV",
      "LB",
      "LI",
      "LT",
      "LU",
      "MO",
      "MK",
      "MY",
      "MT",
      "MC",
      "MA",
      "NL",
      "AN",
      "NO",
      "OM",
      "PK",
      "PE",
      "PH",
      "PL",
      "PT",
      "QA",
      "RO",
      "RU",
      "SA",
      "SG",
      "SK",
      "SI",
      "ZA",
      "ES",
      "LK",
      "SE",
      "CH",
      "TH",
      "TT",
      "TN",
      "TR",
      "UA",
      "AE",
      "GB",
      "UY",
      "VN",
      "ZW",
      "BD"
    ],
    "mkkc": [
      "JP"
    ],
    "apld": [],
    "etsic": [
      "BZ",
      "BO",
      "NZ",
      "VE"
    ],
    "fcca": [
      "CA",
      "CO",
      "DO",
      "GT",
      "MX",
      "PA",
      "PR",
      "TW",
      "US",
      "UZ"
    ]
  },
  "countryCode_5g": {
    "one": {
      "codes": [
        "AL",
        "AI",
        "AW",
        "AT",
        "BY",
        "BM",
        "BA",
        "BW",
        "IO",
        "BG",
        "CV",
        "HR",
        "CY",
        "CZ",
        "DK",
        "EE",
        "FI",
        "FR",
        "GF",
        "PF",
        "FO",
        "TF",
        "GI",
        "DE",
        "GR",
        "GP",
        "GG",
        "HU",
        "IS",
        "IE",
        "IT",
        "KE",
        "LA",
        "LV",
        "LS",
        "LI",
        "LT",
        "LU",
        "MK",
        "MT",
        "IM",
        "MQ",
        "MR",
        "MU",
        "YT",
        "MC",
        "ME",
        "MS",
        "NL",
        "AN",
        "NO",
        "OM",
        "PL",
        "PT",
        "RE",
        "RO",
        "SM",
        "SN",
        "RS",
        "SK",
        "SI",
        "ZA",
        "ES",
        "SE",
        "CH",
        "TC",
        "UG",
        "GB",
        "VG",
        "WF",
        "ZM",
        "AF",
        "JO",
        "MA",
        "EH",
        "EU",
        "DZ",
        "IL",
        "MX",
        "PM",
        "TN",
        "TR",
        "JP"
      ],
      "channels": [
        36,
        40,
        44,
        48
      ]
    },
    "two": {
      "codes": [
        "AS",
        "AG",
        "AZ",
        "BR",
        "KH",
        "KY",
        "CO",
        "CR",
        "DM",
        "DO",
        "EC",
        "GH",
        "GD",
        "HK",
        "KZ",
        "KI",
        "FM",
        "MZ",
        "NA",
        "NZ",
        "NI",
        "NE",
        "PW",
        "PE",
        "PH",
        "PR",
        "VC",
        "TH",
        "TT",
        "UY",
        "ZW",
        "AU",
        "BH",
        "BB",
        "CA",
        "CL",
        "CX",
        "EG",
        "SV",
        "GT",
        "HT",
        "IN",
        "MY",
        "NF",
        "PA",
        "PG",
        "SG",
        "US",
        "VN"
      ],
      "channels": [
        36,
        40,
        44,
        48,
        149,
        153,
        157,
        161,
        165
      ]
    },
    "three": {
      "codes": [
        "LB",
        "MW",
        "MO",
        "QA"
      ],
      "channels": [
        149,
        153,
        157,
        161
      ]
    },
    "four": {
      "codes": [
        "BD",
        "BF",
        "HN",
        "JM",
        "PK",
        "PY",
        "KN",
        "AR",
        "TW",
        "NG"
      ],
      "channels": [
        149,
        153,
        157,
        161,
        165
      ]
    },
    "five": {
      "codes": [
        "SA"
      ],
      "channels": [
        36,
        40,
        44,
        48,
        149,
        153,
        157,
        161
      ]
    },
    "six": {
      "codes": [
        "CN"
      ],
      "channels": [
        36,
        40,
        44,
        48,
        52,
        56,
        60,
        64,
        149,
        153,
        157,
        161,
        165
      ]
    }
  },
  "countries": {
    "NONE": "NONE",
    "AL": "SHQIPERI",
    "DZ": "الجزائر",
    "AR": "ARGENTINA",
    "AM": "ՀԱՅԱՍՏԱՆ",
    "AU": "AUSTRALIA",
    "AT": "ÖSTERREICH",
    "AZ": "AZƏRBAYCAN",
    "BD": "বাংলাদেশ",
    "BH": "البحرين",
    "BY": "БЕЛАРУСЬ",
    "BE": "BELGIË",
    "BA": "БОСНА И ХЕРЦЕГОВИНА",
    "BR": "BRASIL",
    "BN": "BRUNEI DARUSSALAM",
    "BG": "БЪЛГАРИЯ",
    "CL": "CHILE",
    "CN": "中国",
    "CR": "COSTA RICA",
    "HR": "HRVATSKA",
    "CY": "ΚΎΠΡΟΣ",
    "CZ": "ČESKÁ REPUBLIKA",
    "DK": "DANMARK",
    "EC": "ECUADOR",
    "EG": "مصر",
    "SV": "EL SALVADOR",
    "EE": "EESTI",
    "FI": "SUOMI",
    "FR": "FRANCE",
    "FO": "EU",
    "GE": "საქართველო",
    "DE": "DEUTSCHLAND",
    "GR": "ΕΛΛΆΔΑ",
    "HN": "HONDURAS",
    "HK": "中國香港",
    "HU": "MAGYARORSZÁG",
    "IS": "ÍSLAND",
    "IN": "INDIA",
    "ID": "INDONESIA",
    "IE": "ÉIRE",
    "IL": "ישראל",
    "IT": "ITALIA",
    "JM": "JAMAICA",
    "JO": "الأردن",
    "KZ": "КАЗАХСТАН",
    "KE": "KENYA",
    "KW": "الكويت",
    "LV": "LATVIJA",
    "LB": "لبنان",
    "LI": "LIECHTENSTEIN",
    "LT": "LIETUVA",
    "LU": "LUXEMBOURG",
    "MO": "中國澳門",
    "MK": "МАКЕДОНИЈА",
    "MY": "MALAYSIA",
    "MT": "MALTA",
    "MC": "MONACO",
    "MA": "المغرب",
    "NL": "NEDERLAND",
    "AN": "NETHERLANDS ANTILLES",
    "NO": "NORGE",
    "OM": "سلطنة عمان",
    "PK": "PAKISTAN",
    "PE": "PERÚ",
    "PH": "PHILIPPINES",
    "PL": "POLSKA",
    "PT": "PORTUGAL",
    "QA": "قطر",
    "RO": "ROMÂNIA",
    "RU": "Российская Федерация",
    "SA": "السعودية",
    "SG": "SINGAPORE",
    "SK": "SLOVENSKÁ REPUBLIKA",
    "SI": "SLOVENIJA",
    "ZA": "SOUTH AFRICA",
    "ES": "ESPAÑA",
    "LK": "SRILANKA",
    "SE": "SVERIGE",
    "CH": "SCHWEIZ",
    "TH": "ประเทศไทย",
    "TT": "TRINIDAD AND TOBAGO",
    "TN": "تونس",
    "TR": "TÜRKİYE",
    "UA": "Україна",
    "AE": "الإمارات العربية المتحدة",
    "GB": "UNITED KINGDOM",
    "UY": "URUGUAY",
    "VN": "VIỆT NAM",
    "ZW": "ZIMBABWE",
    "JP": "日本",
    "BZ": "BELIZE",
    "BO": "BOLIVIA",
    "NZ": "NEW ZEALAND",
    "VE": "REPÚBLICA BOLIVARIANA DE VENEZUELA",
    "CA": "CANADA",
    "CO": "COLOMBIA",
    "DO": "REPÚBLICA DOMINICANA",
    "GT": "GUATEMALA",
    "MX": "MEXICO",
    "PA": "PANAMÁ",
    "PR": "PUERTO RICO",
    "TW": "中國台灣",
    "US": "UNITED STATES",
    "UZ": "O’zbekiston"
  },
  "countries_5g": {
    "NONE": "NONE",
    "AR": "ARGENTINA",
    "AM": "ՀԱՅԱՍՏԱՆ",
    "AU": "AUSTRALIA",
    "AT": "ÖSTERREICH",
    "AZ": "AZƏRBAYCAN",
    "BH": "البحرين",
    "BY": "БЕЛАРУСЬ",
    "BE": "BELGIË",
    "BA": "БОСНА И ХЕРЦЕГОВИНА",
    "BR": "BRASIL",
    "BN": "BRUNEI DARUSSALAM",
    "BG": "БЪЛГАРИЯ",
    "CL": "CHILE",
    "CN": "中国",
    "CR": "COSTA RICA",
    "HR": "HRVATSKA",
    "CY": "ΚΎΠΡΟΣ",
    "CZ": "ČESKÁ REPUBLIKA",
    "DK": "DANMARK",
    "EC": "ECUADOR",
    "EG": "مصر",
    "SV": "EL SALVADOR",
    "EE": "EESTI",
    "FI": "SUOMI",
    "FR": "FRANCE",
    "FO": "EU",
    "GE": "საქართველო",
    "DE": "DEUTSCHLAND",
    "GR": "ΕΛΛΆΔΑ",
    "HK": "中國香港",
    "HU": "MAGYARORSZÁG",
    "IS": "ÍSLAND",
    "IN": "INDIA",
    "ID": "INDONESIA",
    "IE": "ÉIRE",
    "IL": "ישראל",
    "IT": "ITALIA",
    "JM": "JAMAICA",
    "JO": "الأردن",
    "LV": "LATVIJA",
    "LI": "LIECHTENSTEIN",
    "LT": "LIETUVA",
    "LU": "LUXEMBOURG",
    "MO": "中國澳門",
    "MY": "MALAYSIA",
    "MT": "MALTA",
    "MC": "MONACO",
    "NL": "NEDERLAND",
    "AN": "Netherlands Antilles",
    "NO": "NORGE",
    "OM": "سلطنة عمان",
    "PE": "PERÚ",
    "PH": "PHILIPPINES",
    "PL": "POLSKA",
    "PT": "PORTUGAL",
    "SA": "السعودية",
    "SG": "SINGAPORE",
    "SK": "SLOVENSKÁ REPUBLIKA",
    "SI": "SLOVENIJA",
    "ZA": "SOUTH AFRICA",
    "ES": "ESPAÑA",
    "LK": "SRILANKA",
    "SE": "SVERIGE",
    "CH": "SCHWEIZ",
    "TT": "TRINIDAD AND TOBAGO",
    "TN": "تونس",
    "TR": "TÜRKİYE",
    "GB": "UNITED KINGDOM",
    "UY": "URUGUAY",
    "JP": "日本",
    "BZ": "BELIZE",
    "BO": "BOLIVIA",
    "NZ": "NEW ZEALAND",
    "VE": "VENEZUELA",
    "CA": "CANADA",
    "CO": "COLOMBIA",
    "DO": "REPÚBLICA DOMINICANA",
    "GT": "GUATEMALA",
    "MX": "MEXICO",
    "PA": "PANAMÁ",
    "PR": "PUERTO RICO",
    "TW": "中國台灣",
    "US": "UNITED STATES",
    "UZ": "O’zbekiston"
  },
  "pppoeModes": [
    {
      "name": "PPPoE",
      "value": "PPPOE"
    },
    {
      "name": "Static",
      "value": "STATIC"
    },
    {
      "name": "DHCP",
      "value": "DHCP"
    }
  ],
  "autoPPPOEModes": [
    {
      "name": "PPPoE",
      "value": "AUTO_PPPOE"
    },
    {
      "name": "DHCP",
      "value": "AUTO_DHCP"
    }
  ],
  "sntpTimeSetMode": [
    {
      "name": "manual",
      "value": "manual"
    },
    {
      "name": "auto",
      "value": "auto"
    }
  ],
  "timeZone": [
    {
      "name": "GMT-12:00",
      "value": "-12"
    },
    {
      "name": "GMT-11:00",
      "value": "-11"
    },
    {
      "name": "GMT-10:00",
      "value": "-10"
    },
    {
      "name": "GMT-09:00",
      "value": "-9"
    },
    {
      "name": "GMT-08:00",
      "value": "-8"
    },
    {
      "name": "GMT-08:00-1",
      "value": "-8-1"
    },
    {
      "name": "GMT-07:00",
      "value": "-7"
    },
    {
      "name": "GMT-07:00-1",
      "value": "-7-1"
    },
    {
      "name": "GMT-07:00-2",
      "value": "-7-2"
    },
    {
      "name": "GMT-06:00",
      "value": "-6"
    },
    {
      "name": "GMT-06:00-1",
      "value": "-6-1"
    },
    {
      "name": "GMT-06:00-2",
      "value": "-6-2"
    },
    {
      "name": "GMT-06:00-3",
      "value": "-6-3"
    },
    {
      "name": "GMT-05:00",
      "value": "-5"
    },
    {
      "name": "GMT-05:00-1",
      "value": "-5-1"
    },
    {
      "name": "GMT-05:00-2",
      "value": "-5-2"
    },
    {
      "name": "GMT-04:00",
      "value": "-4"
    },
    {
      "name": "GMT-04:00-1",
      "value": "-4-1"
    },
    {
      "name": "GMT-04:00-2",
      "value": "-4-2"
    },
    {
      "name": "GMT-04:00-3",
      "value": "-4-3"
    },
    {
      "name": "GMT-03:30",
      "value": "-3.5"
    },
    {
      "name": "GMT-03:00",
      "value": "-3"
    },
    {
      "name": "GMT-03:00-1",
      "value": "-3-1"
    },
    {
      "name": "GMT-03:00-2",
      "value": "-3-2"
    },
    {
      "name": "GMT-02:00",
      "value": "-2"
    },
    {
      "name": "GMT-01:00",
      "value": "-1"
    },
    {
      "name": "GMT-01:00-1",
      "value": "-1-1"
    },
    {
      "name": "GMT",
      "value": "0"
    },
    {
      "name": "GMT-1",
      "value": "0-1"
    },
    {
      "name": "GMT+01:00",
      "value": "1"
    },
    {
      "name": "GMT+01:00-1",
      "value": "1-1"
    },
    {
      "name": "GMT+01:00-2",
      "value": "1-2"
    },
    {
      "name": "GMT+01:00-3",
      "value": "1-3"
    },
    {
      "name": "GMT+01:00-4",
      "value": "1-4"
    },
    {
      "name": "GMT+02:00",
      "value": "2"
    },
    {
      "name": "GMT+02:00-1",
      "value": "2-1"
    },
    {
      "name": "GMT+02:00-2",
      "value": "2-2"
    },
    {
      "name": "GMT+02:00-3",
      "value": "2-3"
    },
    {
      "name": "GMT+02:00-4",
      "value": "2-4"
    },
    {
      "name": "GMT+02:00-5",
      "value": "2-5"
    },
    {
      "name": "GMT+02:00-6",
      "value": "2-6"
    },
    {
      "name": "GMT+02:00-7",
      "value": "2-7"
    },
    {
      "name": "GMT+02:00-8",
      "value": "2-8"
    },
    {
      "name": "GMT+03:00",
      "value": "3"
    },
    {
      "name": "GMT+03:00-1",
      "value": "3-1"
    },
    {
      "name": "GMT+03:00-2",
      "value": "3-2"
    },
    {
      "name": "GMT+03:00-3",
      "value": "3-3"
    },
    {
      "name": "GMT+03:00-4",
      "value": "3-4"
    },
    {
      "name": "GMT+04:00",
      "value": "4"
    },
    {
      "name": "GMT+04:00-1",
      "value": "4-1"
    },
    {
      "name": "GMT+04:00-2",
      "value": "4-2"
    },
    {
      "name": "GMT+04:30",
      "value": "4.5"
    },
    {
      "name": "GMT+05:00",
      "value": "5"
    },
    {
      "name": "GMT+05:00-1",
      "value": "5-1"
    },
    {
      "name": "GMT+05:30",
      "value": "5.5"
    },
    {
      "name": "GMT+05:30-1",
      "value": "5.5-1"
    },
    {
      "name": "GMT+05:45",
      "value": "5.75"
    },
    {
      "name": "GMT+06:00",
      "value": "6"
    },
    {
      "name": "GMT+06:00-1",
      "value": "6-1"
    },
    {
      "name": "GMT+06:30",
      "value": "6.5"
    },
    {
      "name": "GMT+07:00",
      "value": "7"
    },
    {
      "name": "GMT+07:00-1",
      "value": "7-1"
    },
    {
      "name": "GMT+08:00",
      "value": "8"
    },
    {
      "name": "GMT+08:00-1",
      "value": "8-1"
    },
    {
      "name": "GMT+08:00-2",
      "value": "8-2"
    },
    {
      "name": "GMT+08:00-3",
      "value": "8-3"
    },
    {
      "name": "GMT+09:00",
      "value": "9"
    },
    {
      "name": "GMT+09:00-1",
      "value": "9-1"
    },
    {
      "name": "GMT+09:00-2",
      "value": "9-2"
    },
    {
      "name": "GMT+09:30",
      "value": "9.5"
    },
    {
      "name": "GMT+09:30-1",
      "value": "9.5-1"
    },
    {
      "name": "GMT+10:00",
      "value": "10"
    },
    {
      "name": "GMT+10:00-1",
      "value": "10-1"
    },
    {
      "name": "GMT+10:00-2",
      "value": "10-2"
    },
    {
      "name": "GMT+10:00-3",
      "value": "10-3"
    },
    {
      "name": "GMT+10:00-4",
      "value": "10-4"
    },
    {
      "name": "GMT+11:00",
      "value": "11"
    },
    {
      "name": "GMT+12:00",
      "value": "12"
    },
    {
      "name": "GMT+12:00-1",
      "value": "12-1"
    },
    {
      "name": "GMT+13:00",
      "value": "13"
    }
  ],
  "daylightSave": [
    {
      "name": "Disable",
      "value": "0"
    },
    {
      "name": "Enable",
      "value": "1"
    }
  ],
  "dstSequences": [
    {
      "name": "First",
      "value": "1"
    },
    {
      "name": "Second",
      "value": "2"
    },
    {
      "name": "Third",
      "value": "3"
    },
    {
      "name": "Fourth",
      "value": "4"
    },
    {
      "name": "Last",
      "value": "5"
    }
  ],
  "dstWeeks": [
    {
      "name": "Sunday",
      "value": "0"
    },
    {
      "name": "Monday",
      "value": "1"
    },
    {
      "name": "Tuesday",
      "value": "2"
    },
    {
      "name": "Wednesday",
      "value": "3"
    },
    {
      "name": "Thursday",
      "value": "4"
    },
    {
      "name": "Friday",
      "value": "5"
    },
    {
      "name": "Saturday",
      "value": "6"
    }
  ],
  "dstMonths": [
    {
      "name": "January",
      "value": "1"
    },
    {
      "name": "February",
      "value": "2"
    },
    {
      "name": "March",
      "value": "3"
    },
    {
      "name": "April",
      "value": "4"
    },
    {
      "name": "May",
      "value": "5"
    },
    {
      "name": "June",
      "value": "6"
    },
    {
      "name": "July",
      "value": "7"
    },
    {
      "name": "August",
      "value": "8"
    },
    {
      "name": "September",
      "value": "9"
    },
    {
      "name": "October",
      "value": "10"
    },
    {
      "name": "November",
      "value": "11"
    },
    {
      "name": "December",
      "value": "12"
    }
  ],
  "wdsModes": [
    {
      "name": "Disable",
      "value": "0"
    },
    {
      "name": "RootAP Mode",
      "value": "1"
    },
    {
      "name": "Bridge Mode",
      "value": "2"
    },
    {
      "name": "Repeater Mode",
      "value": "3"
    }
  ],
  "voipSipDtmfMethod": [
    {
      "name": "InBand",
      "value": "2"
    },
    {
      "name": "RFC2833",
      "value": "3"
    },
    {
      "name": "SIPInfo",
      "value": "4"
    }
  ],
  "sipEncodeMethod": [
    {
      "name": "G.711 u-Law",
      "value": "0"
    },
    {
      "name": "G.711 a-Law",
      "value": "1"
    },
    {
      "name": "G.722",
      "value": "2"
    },
    {
      "name": "G.729",
      "value": "3"
    },
    {
      "name": "G.726-16kps",
      "value": "4"
    },
    {
      "name": "G.726-24kps",
      "value": "5"
    },
    {
      "name": "G.726-32kps",
      "value": "6"
    },
    {
      "name": "G.726-40kps",
      "value": "7"
    }
  ],
  "FORWARDING_MODES": [
    {
      "name": "Unconditional forwarding",
      "value": "1"
    },
    {
      "name": "busy/no answer",
      "value": "0"
    }
  ],
  "TIME_UNITS": [
    {
      "name": "hour",
      "value": "60"
    },
    {
      "name": "Minutes",
      "value": "1"
    }
  ],
  "LTE_FREQUENCY_CODE": [
    {
      "name": "1",
      "value": "2100"
    },
    {
      "name": "2",
      "value": "1900"
    },
    {
      "name": "3",
      "value": "1800"
    },
    {
      "name": "4",
      "value": "1700/2100"
    },
    {
      "name": "5",
      "value": "850"
    },
    {
      "name": "6",
      "value": "800"
    },
    {
      "name": "7",
      "value": "2600"
    },
    {
      "name": "8",
      "value": "900"
    },
    {
      "name": "12",
      "value": "700"
    },
    {
      "name": "13",
      "value": "700"
    },
    {
      "name": "14",
      "value": "700"
    },
    {
      "name": "17",
      "value": "700"
    },
    {
      "name": "20",
      "value": "DD800"
    },
    {
      "name": "25",
      "value": "1900"
    },
    {
      "name": "28",
      "value": "AC700"
    },
    {
      "name": "38",
      "value": "2600"
    },
    {
      "name": "39",
      "value": "1900"
    },
    {
      "name": "40",
      "value": "2300"
    },
    {
      "name": "41",
      "value": "2600"
    }
  ],
  "restartScheduleRules": [
    {
      "name": "week",
      "value": "1"
    },
    {
      "name": "day",
      "value": "2"
    }
  ],
  "restartScheduleHours": [
    {
      "name": "00-01",
      "value": "00"
    },
    {
      "name": "01-02",
      "value": "01"
    },
    {
      "name": "02-03",
      "value": "02"
    },
    {
      "name": "03-04",
      "value": "03"
    },
    {
      "name": "04-05",
      "value": "04"
    },
    {
      "name": "05-06",
      "value": "05"
    },
    {
      "name": "06-07",
      "value": "06"
    },
    {
      "name": "07-08",
      "value": "07"
    },
    {
      "name": "08-09",
      "value": "08"
    },
    {
      "name": "09-10",
      "value": "09"
    },
    {
      "name": "10-11",
      "value": "10"
    },
    {
      "name": "11-12",
      "value": "11"
    },
    {
      "name": "12-13",
      "value": "12"
    },
    {
      "name": "13-14",
      "value": "13"
    },
    {
      "name": "14-15",
      "value": "14"
    },
    {
      "name": "15-16",
      "value": "15"
    },
    {
      "name": "16-17",
      "value": "16"
    },
    {
      "name": "17-18",
      "value": "17"
    },
    {
      "name": "18-19",
      "value": "18"
    },
    {
      "name": "19-20",
      "value": "19"
    },
    {
      "name": "20-21",
      "value": "20"
    },
    {
      "name": "21-22",
      "value": "21"
    },
    {
      "name": "22-23",
      "value": "22"
    },
    {
      "name": "23-00",
      "value": "23"
    }
  ],
  "ACTIVE_TIME_MODES": [
    {
      "name": "Free",
      "value": "0"
    },
    {
      "name": "2 hours",
      "value": "2"
    },
    {
      "name": "4 hours",
      "value": "4"
    },
    {
      "name": "8 hours",
      "value": "8"
    },
    {
      "name": "12 hours",
      "value": "12"
    }
  ],
  "MESH_INDICATOR_LIGHT_MODES": [
    {
      "name": "indicator_light_on",
      "value": "0"
    },
    {
      "name": "indicator_light_off",
      "value": "1"
    }
  ],
  "RESTART_TIME": [
    {
      "name": "00:00-02:00",
      "value": "0"
    },
    {
      "name": "02:00-04:00",
      "value": "2"
    },
    {
      "name": "04:00-06:00",
      "value": "4"
    },
    {
      "name": "06:00-08:00",
      "value": "6"
    },
    {
      "name": "08:00-10:00",
      "value": "8"
    },
    {
      "name": "10:00-12:00",
      "value": "10"
    },
    {
      "name": "12:00-14:00",
      "value": "12"
    },
    {
      "name": "14:00-16:00",
      "value": "14"
    },
    {
      "name": "16:00-18:00",
      "value": "16"
    },
    {
      "name": "18:00-20:00",
      "value": "18"
    },
    {
      "name": "20:00-22:00",
      "value": "20"
    },
    {
      "name": "22:00-24:00",
      "value": "22"
    }
  ],
  "WDS_SUPPORT": false,
  "sysLogModes": [
    {
      "name": "ALL",
      "value": "all"
    },
    {
      "name": "WAN Connect",
      "value": "wan_connect"
    },
    {
      "name": "SMS",
      "value": "sms"
    },
    {
      "name": "WLAN",
      "value": "wlan"
    },
    {
      "name": "Router",
      "value": "router"
    }
  ],
  "VPN_TYPE_MODES": [
    {
      "name": "PPTP",
      "value": "pptp"
    },
    {
      "name": "L2TP",
      "value": "l2tp"
    }
  ],
  "FREQUENCY_TYPE_4G": [
    {
      "name": "all",
      "value": "0xA3E2AB0908DF"
    },
    {
      "name": "1",
      "value": "0x000000001"
    },
    {
      "name": "2",
      "value": "0x000000002"
    },
    {
      "name": "3",
      "value": "0x000000004"
    },
    {
      "name": "4",
      "value": "0x000000008"
    },
    {
      "name": "5",
      "value": "0x000000010"
    },
    {
      "name": "7",
      "value": "0x000000040"
    },
    {
      "name": "8",
      "value": "0x000000080"
    },
    {
      "name": "12",
      "value": "0x000000800"
    },
    {
      "name": "17",
      "value": "0x000010000"
    },
    {
      "name": "20",
      "value": "0x000080000"
    },
    {
      "name": "25",
      "value": "0x001000000"
    },
    {
      "name": "26",
      "value": "0x002000000"
    },
    {
      "name": "28",
      "value": "0x008000000"
    },
    {
      "name": "30",
      "value": "0x020000000"
    },
    {
      "name": "32",
      "value": "0x080000000"
    },
    {
      "name": "34",
      "value": "0x200000000"
    },
    {
      "name": "38",
      "value": "0x2000000000"
    },
    {
      "name": "39",
      "value": "0x4000000000"
    },
    {
      "name": "40",
      "value": "0x8000000000"
    },
    {
      "name": "41",
      "value": "0x10000000000"
    },
    {
      "name": "42",
      "value": "0x20000000000"
    },
    {
      "name": "46",
      "value": "0x200000000000"
    },
    {
      "name": "48",
      "value": "0x800000000000"
    }
  ],
  "ddns_Modeselect": [
    {
      "name": "manual",
      "value": "manual"
    },
    {
      "name": "auto",
      "value": "auto"
    }
  ],
  "NFC_SYNC_WIFIAP": [
    {
      "name": "chip1_main",
      "value": "1"
    },
    {
      "name": "chip2_main",
      "value": "2"
    },
    {
      "name": "chip1_guest",
      "value": "3"
    },
    {
      "name": "chip2_guest",
      "value": "4"
    }
  ],
  "NFC_SYNC_WIFIAP_MAIN": [
    {
      "name": "chip1_main",
      "value": "1"
    },
    {
      "name": "chip2_main",
      "value": "2"
    }
  ],
  "NFC_SYNC_WIFISINGLEAP": [
    {
      "name": "chip_main",
      "value": "1"
    },
    {
      "name": "chip_guest",
      "value": "3"
    }
  ],
  "NFC_SYNC_WIFISINGLEAP_MAIN": [
    {
      "name": "chip_main",
      "value": "1"
    }
  ],
  "DDNSDDP": [
    {
      "name": "dyndns.org",
      "value": "dyndns.org"
    },
    {
      "name": "freedns.afraid.org",
      "value": "freedns.afraid.org"
    },
    {
      "name": "zoneedit.com",
      "value": "zoneedit.com"
    },
    {
      "name": "no-ip.com",
      "value": "no-ip.com"
    },
    {
      "name": "None",
      "value": ""
    }
  ]
}
```

```
http://192.168.32.1/i18n/Messages_en.properties?v=1643166201485
http://192.168.32.1/index.html#login
http://192.168.32.1/index.html#change_password
http://192.168.32.1/index.html#mesh_building
http://192.168.32.1/index.html#home
http://192.168.32.1/index.html#status
http://192.168.32.1/index.html#sms
http://192.168.32.1/index.html#phonebook
http://192.168.32.1/index.html#parental_control
http://192.168.32.1/index.html#setting
http://192.168.32.1/index.html#suggested_position
http://192.168.32.1/index.html#internet_setting
http://192.168.32.1/index.html#mesh_network_setting
http://192.168.32.1/index.html#mesh_switch_wifi_setting
http://192.168.32.1/index.html#mesh_faq
http://192.168.32.1/index.html#mesh_add_sub_node
http://192.168.32.1/index.html#net_setting
http://192.168.32.1/index.html#wifi
http://192.168.32.1/index.html#device_setting
http://192.168.32.1/index.html#firewall
http://192.168.32.1/index.html#router_setting
http://192.168.32.1/index.html#bind_addr_autocomplete
http://192.168.32.1/index.html#bind_addr_lan
http://192.168.32.1/index.html#group_all
http://192.168.32.1/index.html#group_common
http://192.168.32.1/index.html#group_family
http://192.168.32.1/index.html#group_friend
http://192.168.32.1/index.html#group_colleague
http://192.168.32.1/index.html#device_info
http://192.168.32.1/index.html#traffic_statistics
http://192.168.32.1/index.html#traffic_alert
http://192.168.32.1/index.html#smslist
http://192.168.32.1/index.html#sms_setting
http://192.168.32.1/index.html#voip_setting
http://192.168.32.1/index.html#dial_setting
http://192.168.32.1/index.html#net_select
http://192.168.32.1/index.html#net_select_hide
http://192.168.32.1/index.html#apn_setting
http://192.168.32.1/index.html#wifi_basic
http://192.168.32.1/index.html#wifi_main
http://192.168.32.1/index.html#wifi_guest
http://192.168.32.1/index.html#station_info
http://192.168.32.1/index.html#wifi_advance
http://192.168.32.1/index.html#black_list
http://192.168.32.1/index.html#wps
http://192.168.32.1/index.html#wifi_main_ssid
http://192.168.32.1/index.html#wifi_mac_filter
http://192.168.32.1/index.html#wifi_main_chip1
http://192.168.32.1/index.html#wifi_main_chip2
http://192.168.32.1/index.html#wifi_beamforming_setting
http://192.168.32.1/index.html#mesh_ssid_setting
http://192.168.32.1/index.html#mesh_device_list
http://192.168.32.1/index.html#mesh_wps
http://192.168.32.1/index.html#wifi_mesh_guest
http://192.168.32.1/index.html#password_management
http://192.168.32.1/index.html#pin_management
http://192.168.32.1/index.html#restore
http://192.168.32.1/index.html#restart
http://192.168.32.1/index.html#SNTP
http://192.168.32.1/index.html#sys_log
http://192.168.32.1/index.html#tr069config
http://192.168.32.1/index.html#port_filter
http://192.168.32.1/index.html#url_filter
http://192.168.32.1/index.html#port_map
http://192.168.32.1/index.html#system_security
http://192.168.32.1/index.html#upnp
http://192.168.32.1/index.html#dmz
http://192.168.32.1/index.html#sleep_mode
http://192.168.32.1/index.html#others
http://192.168.32.1/index.html#pc_children_group
http://192.168.32.1/index.html#pc_time_limited
http://192.168.32.1/index.html#ota_update
http://192.168.32.1/index.html#network_info
http://192.168.32.1/index.html#diagnosis
http://192.168.32.1/index.html#thermal_switch
http://192.168.32.1/index.html#ping_log
http://192.168.32.1/index.html#sleep_protection_mode
http://192.168.32.1/index.html#restart_schedule
http://192.168.32.1/index.html#vpn_client
http://192.168.32.1/index.html#user_details
http://192.168.32.1/index.html#supplement_service
http://192.168.32.1/index.html#voip_settings
http://192.168.32.1/index.html#voip_advanced_settings
http://192.168.32.1/index.html#ap_station
http://192.168.32.1/index.html#temp_status
http://192.168.32.1/index.html#ant_switch
http://192.168.32.1/index.html#debug_page
http://192.168.32.1/index.html#rf_mmw
http://192.168.32.1/index.html#configuragtion
http://192.168.32.1/index.html#wifiChannel
http://192.168.32.1/index.html#wifiChannel5g
http://192.168.32.1/index.html#nfc_setting
http://192.168.32.1/index.html#ddns_settings
http://192.168.32.1/index.html#offline_info
http://192.168.32.1/index.html#port_trigger
http://192.168.32.1/index.html#sysInfo
http://192.168.32.1/index.html#lock_band_setting
http://192.168.32.1/index.html#change_mode
http://192.168.32.1/index.html#external_antenna
http://192.168.32.1/index.html#dns_settings
http://192.168.32.1/index.html#ulca_setting
```

#### Sorted by admin

 cat a|grep -i adm -B2|grep -v path |grep -i hash  |sed 's/: "/http:\/\/192.168.32.1\/index.html/g'  |grep -o 'http.*"'  |cut -d'"' -f1
http://192.168.32.1/index.html#device_setting
http://192.168.32.1/index.html#router_setting
http://192.168.32.1/index.html#bind_addr_autocomplete
http://192.168.32.1/index.html#bind_addr_lan
http://192.168.32.1/index.html#password_management
http://192.168.32.1/index.html#pin_management
http://192.168.32.1/index.html#restore
http://192.168.32.1/index.html#restart
http://192.168.32.1/index.html#SNTP
http://192.168.32.1/index.html#sys_log
http://192.168.32.1/index.html#tr069config
http://192.168.32.1/index.html#others
http://192.168.32.1/index.html#network_info
http://192.168.32.1/index.html#diagnosis
http://192.168.32.1/index.html#thermal_switch
http://192.168.32.1/index.html#sleep_protection_mode
http://192.168.32.1/index.html#restart_schedule
http://192.168.32.1/index.html#configuragtion
http://192.168.32.1/index.html#ddns_settings
http://192.168.32.1/index.html#sysInfo
http://192.168.32.1/index.html#external_antenna
http://192.168.32.1/index.html#dns_settings

### Sorted by netwwork

cat a|grep -i network -B2|grep -v path |grep -i hash  |sed 's/: "/http:\/\/192.168.32.1\/index.html/g'  |grep -o 'http.*"'  |cut -d'"' -f1
http://192.168.32.1/index.html#setting
http://192.168.32.1/index.html#internet_setting
http://192.168.32.1/index.html#mesh_network_setting
http://192.168.32.1/index.html#net_setting
http://192.168.32.1/index.html#dial_setting
http://192.168.32.1/index.html#net_select
http://192.168.32.1/index.html#net_select_hide
http://192.168.32.1/index.html#apn_setting
http://192.168.32.1/index.html#mesh_device_list
http://192.168.32.1/index.html#network_info
http://192.168.32.1/index.html#lock_band_setting
http://192.168.32.1/index.html#ulca_setting


### Sorted by hide

http://192.168.32.1/index.html#net_select_hide
http://192.168.32.1/index.html#ant_switch
http://192.168.32.1/index.html#debug_page
http://192.168.32.1/index.html#rf_mmw

### Sorted by nfc

http://192.168.32.1/index.html#nfc_setting

### Sorted by firewwall

http://192.168.32.1/index.html#parental_control
http://192.168.32.1/index.html#firewall
http://192.168.32.1/index.html#port_filter
http://192.168.32.1/index.html#url_filter
http://192.168.32.1/index.html#port_map
http://192.168.32.1/index.html#system_security
http://192.168.32.1/index.html#upnp
http://192.168.32.1/index.html#dmz
http://192.168.32.1/index.html#pc_children_group
http://192.168.32.1/index.html#pc_time_limited
http://192.168.32.1/index.html#vpn_client
http://192.168.32.1/index.html#offline_info
http://192.168.32.1/index.html#port_trigger

### Sorted by wifi

http://192.168.32.1/index.html#mesh_switch_wifi_setting
http://192.168.32.1/index.html#wifi
http://192.168.32.1/index.html#wifi_basic
http://192.168.32.1/index.html#wifi_main
http://192.168.32.1/index.html#wifi_guest
http://192.168.32.1/index.html#station_info
http://192.168.32.1/index.html#wifi_advance
http://192.168.32.1/index.html#black_list
http://192.168.32.1/index.html#wps
http://192.168.32.1/index.html#wifi_main_ssid
http://192.168.32.1/index.html#wifi_mac_filter
http://192.168.32.1/index.html#wifi_main_chip1
http://192.168.32.1/index.html#wifi_main_chip2
http://192.168.32.1/index.html#wifi_beamforming_setting
http://192.168.32.1/index.html#mesh_ssid_setting
http://192.168.32.1/index.html#mesh_wps
http://192.168.32.1/index.html#wifi_mesh_guest
http://192.168.32.1/index.html#sleep_mode
http://192.168.32.1/index.html#ap_station
http://192.168.32.1/index.html#wifiChannel
http://192.168.32.1/index.html#wifiChannel5

### Sorted by voip

http://192.168.32.1/index.html#voip_setting
http://192.168.32.1/index.html#user_details
http://192.168.32.1/index.html#supplement_service
http://192.168.32.1/index.html#voip_settings
http://192.168.32.1/index.html#voip_advanced_settings


### Awesome resources

* ![4G-Hotspots-For-Fun-Bugs-Net-Financial-Loss.pdf](https://github.com/wuseman/ZTE-MF297D_Nordic1_B0X-WPA3/files/9388063/4G-Hotspots-For-Fun-Bugs-Net-Financial-Loss.pdf)


* ![ZTE_Vulnerability.pdf](https://github.com/wuseman/ZTE-MF297D_Nordic1_B0X-WPA3/files/9388064/ZTE_Vulnerability.pdf)

# json files

[menu.txt](https://github.com/wuseman/ZTE-MF297D_Nordic1_B0X-WPA3/files/9388066/menu.txt)

[menu4g.js.txt](https://github.com/wuseman/ZTE-MF297D_Nordic1_B0X-WPA3/files/9388065/menu4g.js.txt)



# SORT BELOW

# ZTE PASS
![zte_pass](https://user-images.githubusercontent.com/26827453/185767755-334ede86-4a52-4ee0-b9ef-b4249d4e86d5.png)
