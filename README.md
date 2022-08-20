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


# TO BE SORTED
![Screen Shot 2022-06-20 at 22 50 35-fullpage](https://user-images.githubusercontent.com/26827453/185767707-50434de7-4168-48a1-81a1-76b5e1328b55.png)
![Screen Shot 2022-06-20 at 22 50 49-fullpage](https://user-images.githubusercontent.com/26827453/185767708-34daf06c-7228-449e-bc3a-02c478f07b4b.png)
![Screen Shot 2022-06-20 at 22 50 57-fullpage](https://user-images.githubusercontent.com/26827453/185767709-f8ea1248-7ffa-46cb-8806-1a5e9fc127e7.png)
![Screen Shot 2022-06-20 at 22 51 08-fullpage](https://user-images.githubusercontent.com/26827453/185767710-672fcb7d-0f81-457b-96c4-3094a1bb664d.png)
![Screen Shot 2022-06-20 at 22 51 11-fullpage](https://user-images.githubusercontent.com/26827453/185767711-04d710a9-e436-4726-aa1b-0ad066b51cf3.png)
![Screen Shot 2022-06-20 at 22 51 14-fullpage](https://user-images.githubusercontent.com/26827453/185767712-410c8c1a-33a0-4852-a7a3-775eb4a2b467.png)
![Screen Shot 2022-06-20 at 22 51 17-fullpage](https://user-images.githubusercontent.com/26827453/185767713-a6c07d7b-1b31-4dc8-9f2a-25c1c3845b40.png)
![Screen Shot 2022-06-20 at 22 51 26-fullpage](https://user-images.githubusercontent.com/26827453/185767714-1f109898-9d4d-4548-9a62-588f41ef56ff.png)
![Screen Shot 2022-06-20 at 22 51 28-fullpage](https://user-images.githubusercontent.com/26827453/185767715-4647dc59-a141-448b-bf7f-19480172d58e.png)
![Screen Shot 2022-06-20 at 22 51 33-fullpage](https://user-images.githubusercontent.com/26827453/185767716-65cc68e5-fec0-4809-a902-c5434be52346.png)
![Screen Shot 2022-06-20 at 22 51 46-fullpage](https://user-images.githubusercontent.com/26827453/185767717-baf6330a-718d-4652-a2ce-838bde86f13d.png)
![Screen Shot 2022-06-20 at 22 51 54-fullpage](https://user-images.githubusercontent.com/26827453/185767718-dd3fec83-2ade-4c1b-b814-faddb3fb86b3.png)
![Screen Shot 2022-06-20 at 22 52 16-fullpage](https://user-images.githubusercontent.com/26827453/185767719-6c3ab0d4-d594-486b-a960-7adac287abc4.png)
![Screen Shot 2022-06-20 at 22 52 22-fullpage](https://user-images.githubusercontent.com/26827453/185767721-280c5f24-27c0-421b-a4ce-a3d74d6dc537.png)
![Screen Shot 2022-06-20 at 22 53 03-fullpage](https://user-images.githubusercontent.com/26827453/185767722-61ec36ef-db09-407c-8f18-5aeed1c7741d.png)
![Screen Shot 2022-06-20 at 22 53 27-fullpage](https://user-images.githubusercontent.com/26827453/185767723-11c23680-5f77-4d85-8be5-741fa1a2883b.png)
![Screen Shot 2022-06-20 at 22 53 32-fullpage](https://user-images.githubusercontent.com/26827453/185767724-e46a78e9-e809-41c8-8e34-1391bc40b12c.png)
![Screen Shot 2022-06-20 at 22 53 37-fullpage](https://user-images.githubusercontent.com/26827453/185767725-a4b9ecf4-663f-48c4-a1ca-c2ccc3bb32de.png)
![Screen Shot 2022-06-20 at 22 54 51-fullpage](https://user-images.githubusercontent.com/26827453/185767727-36599d36-28a3-471d-802f-9c42cc5ddb04.png)
![screencapture-192-168-1-100-gateway-lp-2022-06-17-14_00_06](https://user-images.githubusercontent.com/26827453/185767728-bc80cfe0-9690-489e-b5ad-b26e8bad05ba.png)
![screencapture-192-168-1-100-gateway-lp-2022-06-17-14_00_12](https://user-images.githubusercontent.com/26827453/185767729-bec54071-3c08-4094-b9d8-0af85d6f952b.png)
![screencapture-192-168-32-1-index-html-2022-06-15-22_22_51](https://user-images.githubusercontent.com/26827453/185767730-0d4f83c3-1223-434a-a7ff-3b4c71d2c83a.png)
![screencapture-192-168-32-1-index-html-2022-06-15-22_22_59](https://user-images.githubusercontent.com/26827453/185767731-d8e9b6bd-2ee3-4ac1-8f8e-0324ac64c531.png)
![screencapture-192-168-32-1-index-html-2022-06-15-22_23_30](https://user-images.githubusercontent.com/26827453/185767732-37f93378-5c0d-48e2-82eb-10bd310c0cc7.png)
![screencapture-192-168-32-1-index-html-2022-06-15-22_24_08](https://user-images.githubusercontent.com/26827453/185767733-184b76b7-2952-4121-9c82-aa38755197f3.png)
![screencapture-192-168-32-1-index-html-2022-06-15-22_24_11](https://user-images.githubusercontent.com/26827453/185767735-52388820-1415-485b-ad9a-cbd7991c5fb3.png)
![screencapture-192-168-32-1-index-html-2022-06-16-02_29_25](https://user-images.githubusercontent.com/26827453/185767736-d44e5a39-df90-4b57-8fed-2dfc70abdc01.png)
![screencapture-192-168-32-1-index-html-2022-06-16-07_13_48](https://user-images.githubusercontent.com/26827453/185767737-26bccc25-8c26-476b-abde-60d4f1c927a4.png)
![screencapture-192-168-32-1-index-html-2022-06-16-07_15_49](https://user-images.githubusercontent.com/26827453/185767738-f85729eb-700c-412f-819c-062ee5a9c620.png)
![screencapture-192-168-32-1-index-html-2022-06-16-07_19_17](https://user-images.githubusercontent.com/26827453/185767739-a36ef14b-bf42-4482-a502-db090a0f6b61.png)
![screencapture-192-168-32-1-index-html-2022-06-16-07_35_43](https://user-images.githubusercontent.com/26827453/185767740-0e42b1fe-6a0a-4d25-826c-9255c01f6da4.png)
![screencapture-192-168-32-1-index-html-2022-06-16-08_02_25](https://user-images.githubusercontent.com/26827453/185767741-e688a885-5725-49d8-89b6-4d44d6cefc8a.png)
![screencapture-192-168-32-1-m-index-html-2022-06-16-02_59_19](https://user-images.githubusercontent.com/26827453/185767742-55fa7402-6f28-45f0-96eb-39c977578b91.png)
![screencapture-192-168-32-1-m-index-html-2022-06-16-07_37_40](https://user-images.githubusercontent.com/26827453/185767743-56ee40dc-2d02-420a-b03d-a823cc15c8ff.png)
![Screenshot_20220620_225407](https://user-images.githubusercontent.com/26827453/185767744-b53dcbe1-c6e8-4a48-9e05-8428f5054a0e.png)
![Screenshot_20220817_182948](https://user-images.githubusercontent.com/26827453/185767745-ab2973ab-7953-4ce7-bde3-498befd238df.png)
![Screenshot_20220817_183037](https://user-images.githubusercontent.com/26827453/185767746-3c8d22bf-b7ee-41f7-acf3-ab5436fdc44a.png)
![Screenshot_20220817_183319](https://user-images.githubusercontent.com/26827453/185767747-f3cc4779-17e4-4051-afa2-650285672322.png)
![Screenshot_20220820_043200](https://user-images.githubusercontent.com/26827453/185767748-0bdcadbc-3809-4ce3-8237-46829962c5b2.png)
 

![#voip_advanced:saettings](https://user-images.githubusercontent.com/26827453/179758051-b6902d42-2f17-46c2-9c75-523bd3c25f31.png)
![#voip_settings](https://user-images.githubusercontent.com/26827453/179758057-3c4191d2-510d-419a-ad15-1f2022f9aacb.png)
![192 168 32 1_4G+ Broadband MF297D_2022-06-11_081924-Screenshot](https://user-images.githubusercontent.com/26827453/179758059-151a5fcf-a2e2-4b44-9860-708665c0dd58.png)
![192 168 32 1_4G+ Broadband MF297D_2022-06-11_083145-Screenshot](https://user-images.githubusercontent.com/26827453/179758060-d07678aa-464d-4a29-bd80-5a41268cd265.png)
![antenna_SWITCH](https://user-images.githubusercontent.com/26827453/179758062-41f56062-cde4-40b3-a7aa-808e8bea350c.png)
![bind_addr_autocomplete](https://user-images.githubusercontent.com/26827453/179758063-10a4e580-df5f-428b-83f2-1f2724ebde8f.png)
![bind_addr](https://user-images.githubusercontent.com/26827453/179758065-dd4d324b-6ff9-408d-ae8f-9ab68751ba09.png)
![configureation](https://user-images.githubusercontent.com/26827453/179758067-cd43f046-5dda-419a-bb9d-2c2e4cefa471.png)
![device_setting](https://user-images.githubusercontent.com/26827453/179758069-7d0858f7-8c3a-46bc-8c74-60731fd92436.png)
![deviceınfo](https://user-images.githubusercontent.com/26827453/179758071-a4f5d494-cf1f-450e-a0c9-7ec36b72e792.png)
![diagnoses](https://user-images.githubusercontent.com/26827453/179758074-e22b2596-2130-4412-80fc-4e10af84c8a6.png)
![dmz](https://user-images.githubusercontent.com/26827453/179758078-c5b616c6-fa39-409b-9f24-de0e1d8f6c03.png)
![external](https://user-images.githubusercontent.com/26827453/179758080-e282b088-0fdb-4622-b33f-1438342cf9e5.png)
![firewall](https://user-images.githubusercontent.com/26827453/179758083-f147ef15-1007-4125-9ac9-c1825b66040a.png)
![home](https://user-images.githubusercontent.com/26827453/179758086-1fab4b5a-c2fc-4291-abfe-6e70fa857847.png)
![mac_filter](https://user-images.githubusercontent.com/26827453/179758087-b50f9eb7-8dc2-4fbf-acc5-4d22d1539bcd.png)
![main](https://user-images.githubusercontent.com/26827453/179758088-e61fec1b-307f-4ab3-9f42-5afde502fd26.png)
![mesh_add_sub_node](https://user-images.githubusercontent.com/26827453/179758090-3daf8b26-4ff4-4b34-8e1e-f66774225ef5.png)
![mesh_device_list](https://user-images.githubusercontent.com/26827453/179758092-1d4dc7e4-dc5d-4b95-a83b-b2d6b9ede6e9.png)
![mesh_faq](https://user-images.githubusercontent.com/26827453/179758094-0605c3b0-1a74-4819-963e-4369ef3bbe85.png)
![mesh_guest](https://user-images.githubusercontent.com/26827453/179758098-773d4e85-ee1b-4c4b-bdcd-5ebafabf3a8f.png)
![mesh_network_setting](https://user-images.githubusercontent.com/26827453/179758100-b0b4641f-9f70-4fd1-8e02-c93c26e0d36d.png)
![mesh_switch_wifi_setting](https://user-images.githubusercontent.com/26827453/179758103-41d56b94-d285-48eb-b418-73761da4480c.png)
![mesh_wps](https://user-images.githubusercontent.com/26827453/179758105-d2d4292f-381f-46e5-8ceb-aeb9e7e2a9b3.png)
![mesh](https://user-images.githubusercontent.com/26827453/179758106-72e81b6f-53b4-47eb-9b6a-b2919bd6870a.png)
![network_info](https://user-images.githubusercontent.com/26827453/179758107-064d9844-1645-44e0-b387-1edd1a7d11df.png)

[MF279_opensource_code.tar.gz](https://github.com/wuseman/ZTE-MF297D_Nordic1_B0X-WPA3/files/9141280/MF279_opensource_code.tar.gz)



![ssg](https://user-images.githubusercontent.com/26827453/179757860-999cdd75-e749-4bcb-921c-f58e28b399d3.png)
![ssh_fail](https://user-images.githubusercontent.com/26827453/179757863-2c7353b5-a51a-4c14-ae63-5ecfe37dfe2f.png)
![ssh](https://user-images.githubusercontent.com/26827453/179757866-8e21b825-b995-4d81-bfa1-82147ad0ea7c.png)
![telnet](https://user-images.githubusercontent.com/26827453/179757869-d2dd4c5c-9fb5-4311-afca-6bd0900bfb86.png)
![tr](https://user-images.githubusercontent.com/26827453/179757873-d45f6c59-872f-4568-b8b2-e05b36a1d893.png)


![1](https://user-images.githubusercontent.com/26827453/179757788-541f5efe-7f4b-417a-bbdc-ebdaa260dc5a.png)
![192 168 32 1_4G+ Broadband M2F297D_2022-06-11-Screenshot](https://user-images.githubusercontent.com/26827453/179757790-dd840354-c77e-4a02-b59a-90533075d8b7.png)
![192 168 32 1_4G+ Broadband MF297D_2022-06-11-Screenshot](https://user-images.githubusercontent.com/26827453/179757793-36bde41f-e4ab-475e-9ae1-87b127c506eb.png)
![192 168 32 1_4G+ Broadband MF297D_2022-11232106-11-Screenshot](https://user-images.githubusercontent.com/26827453/179757796-622a539a-38f8-46cf-a404-30dbe43ea49b.png)
![12435](https://user-images.githubusercontent.com/26827453/179757798-1b3dad78-663c-43a6-bd13-4e338b25a6e5.png)
![backup_file](https://user-images.githubusercontent.com/26827453/179757804-852439f4-3eb5-4563-bb34-73ff6cd72bc6.png)
![easy](https://user-images.githubusercontent.com/26827453/179757805-105f722b-2ef0-47e9-9e87-33e3b844c9b4.png)
![get](https://user-images.githubusercontent.com/26827453/179757806-5fb413d7-5d53-438f-b12c-01eb7b2567e8.png)
![Screenshot_20220611_040858](https://user-images.githubusercontent.com/26827453/179757808-1ae5f2c5-6d4d-422d-b967-fcce848023c2.png)
![Screenshot_20220611_060724](https://user-images.githubusercontent.com/26827453/179757810-fb978c29-5c0e-4b12-b5aa-b63b1d4b160e.png)
![Screenshot_20220611_060750](https://user-images.githubusercontent.com/26827453/179757811-8c9e32db-2135-493b-bd36-15bd977f214d.png)
![Screenshot_20220611_060816](https://user-images.githubusercontent.com/26827453/179757816-dc964ca1-f484-40f8-9db8-53b381ed59fa.png)
![Screenshot_20220611_060843](https://user-images.githubusercontent.com/26827453/179757818-fd76889c-f76a-4c6c-a9a6-746dcdf74de0.png)
![Screenshot_20220611_060951](https://user-images.githubusercontent.com/26827453/179757820-71cc2336-dbe6-43c4-ba6f-70bb78b97651.png)
![Screenshot_20220611_061009](https://user-images.githubusercontent.com/26827453/179757823-fe6bd418-7174-4b40-ac78-21fb41c422fb.png)
![Screenshot_20220611_061026](https://user-images.githubusercontent.com/26827453/179757826-6bb0c853-60f8-48e1-bba9-fc24553f2a48.png)
![Screenshot_20220611_061305](https://user-images.githubusercontent.com/26827453/179757829-cbe33fb8-9f39-4517-be87-9087b24be06d.png)
![Screenshot_20220611_062743](https://user-images.githubusercontent.com/26827453/179757831-74c2537a-d368-4360-be51-a937496fca3e.png)
![Screenshot_20220611_063545](https://user-images.githubusercontent.com/26827453/179757833-7e564e60-56d9-4e08-8679-5d440881f3d9.png)
![Screenshot_20220611_063908](https://user-images.githubusercontent.com/26827453/179757834-1bc429d3-6d6c-4992-b344-5cb99bc1b749.png)
![Screenshot_20220611_064205](https://user-images.githubusercontent.com/26827453/179757836-ec7e4d71-c58e-4450-ba85-dd2e7a3f5645.png)
![Screenshot_20220611_064331](https://user-images.githubusercontent.com/26827453/179757839-61d4bce5-3c77-40d3-bc89-c5a4eb29c88b.png)
![Screenshot_20220611_064821](https://user-images.githubusercontent.com/26827453/179757842-7500118c-6589-45ef-8d8f-66c9658e2cd2.png)
![Screenshot_20220611_073812](https://user-images.githubusercontent.com/26827453/179757843-34819555-c429-49c6-b284-005a754b8707.png)
![Screenshot_20220611_075512](https://user-images.githubusercontent.com/26827453/179757845-9a5d8e04-0c5b-4d91-8b9f-36d0d2e216a5.png)
![Screenshot_20220611_080138](https://user-images.githubusercontent.com/26827453/179757846-59b08319-61b3-4d3e-8325-0c1f77047bf6.png)
![Screenshot_20220611_084042](https://user-images.githubusercontent.com/26827453/179757848-1c9ff63c-78a5-4f96-8c92-05f1ec40e31a.png)
![Screenshot_20220611_103142](https://user-images.githubusercontent.com/26827453/179757850-39ee3d12-ee48-4734-9cc4-14c332834b78.png)
![Screenshot_20220611_110751](https://user-images.githubusercontent.com/26827453/179757851-9e175c89-fe30-4b33-ab53-0889fac3b980.png)
![Screenshot_20220611_113039](https://user-images.githubusercontent.com/26827453/179757853-5955531f-ea36-47ef-a439-6916cdce1391.png)
![Screenshot_20220611_122045](https://user-images.githubusercontent.com/26827453/179757856-a51085c7-c383-4ccf-bd10-86e86dc6d1f8.png)
![source](https://user-images.githubusercontent.com/26827453/179757859-32c3dd73-486b-48c8-b229-cd6654e9769b.png)





[MF279_opensource_code.tar.gz](https://github.com/wuseman/ZTE-MF297D_Nordic1_B0X-WPA3/files/9141262/MF279_opensource_code.tar.gz)

![Screen Shot 2022-06-20 at 22 50 35-fullpage](https://user-images.githubusercontent.com/26827453/179757438-bcdbd0cf-1856-4a4e-b86c-37f45d560cf0.png)
![Screen Shot 2022-06-20 at 22 50 49-fullpage](https://user-images.githubusercontent.com/26827453/179757440-41512350-95b0-4263-bdb7-c2faf9472ba3.png)
![Screen Shot 2022-06-20 at 22 50 57-fullpage](https://user-images.githubusercontent.com/26827453/179757444-b890b36b-561e-4262-8349-d49490ab4aeb.png)
![Screen Shot 2022-06-20 at 22 51 08-fullpage](https://user-images.githubusercontent.com/26827453/179757446-779cf043-da60-4b39-bf53-aa360d457d27.png)
![Screen Shot 2022-06-20 at 22 51 11-fullpage](https://user-images.githubusercontent.com/26827453/179757448-51626de4-75f2-4975-9957-6e77ef1037e8.png)
![Screen Shot 2022-06-20 at 22 51 14-fullpage](https://user-images.githubusercontent.com/26827453/179757451-623f5675-9ec0-4f35-905d-daf20368a858.png)
![Screen Shot 2022-06-20 at 22 51 17-fullpage](https://user-images.githubusercontent.com/26827453/179757455-6ecd31fc-263b-433a-ad3b-4dbcf7e1c9aa.png)
![Screen Shot 2022-06-20 at 22 51 26-fullpage](https://user-images.githubusercontent.com/26827453/179757460-b4cb19b7-179d-4fec-b30c-36358d4e068c.png)
![Screen Shot 2022-06-20 at 22 51 28-fullpage](https://user-images.githubusercontent.com/26827453/179757464-8c722afa-0a90-4194-9f82-8f32f9db307d.png)
![Screen Shot 2022-06-20 at 22 51 33-fullpage](https://user-images.githubusercontent.com/26827453/179757465-d90afafe-2e7b-4d01-9ed2-07d00c822575.png)
![Screen Shot 2022-06-20 at 22 51 46-fullpage](https://user-images.githubusercontent.com/26827453/179757467-5e6c9970-362e-4b1e-8f79-6402366fa00d.png)
![Screen Shot 2022-06-20 at 22 51 54-fullpage](https://user-images.githubusercontent.com/26827453/179757468-054b8d6c-37be-4700-beef-c31b524e9864.png)
![Screen Shot 2022-06-20 at 22 52 16-fullpage](https://user-images.githubusercontent.com/26827453/179757470-1fdf6f39-6f7b-4b20-88d1-3c8c7275de4f.png)
![Screen Shot 2022-06-20 at 22 52 22-fullpage](https://user-images.githubusercontent.com/26827453/179757472-c719cfd6-d47d-4362-b84e-cc7a07de8d91.png)
![Screen Shot 2022-06-20 at 22 53 03-fullpage](https://user-images.githubusercontent.com/26827453/179757474-af9e0395-c7fa-4365-b153-67b3e696d5c5.png)
![Screen Shot 2022-06-20 at 22 53 27-fullpage](https://user-images.githubusercontent.com/26827453/179757478-f053e628-37e8-4844-966c-5a759cd3d63d.png)
![Screen Shot 2022-06-20 at 22 53 32-fullpage](https://user-images.githubusercontent.com/26827453/179757479-87529974-f3ee-4b6e-8827-875ef7865ba7.png)
![Screen Shot 2022-06-20 at 22 53 37-fullpage](https://user-images.githubusercontent.com/26827453/179757480-c506aaaf-e598-4158-ae62-c032c744180c.png)
![Screen Shot 2022-06-20 at 22 54 51-fullpage](https://user-images.githubusercontent.com/26827453/179757483-9e7e192e-d0c8-4a2b-bf19-cd5cf2c506d7.png)
![screencapture-192-168-1-100-gateway-lp-2022-06-17-14_00_06](https://user-images.githubusercontent.com/26827453/179757484-0db17c78-7366-4c0a-9b23-08120c9653a8.png)
![screencapture-192-168-1-100-gateway-lp-2022-06-17-14_00_12](https://user-images.githubusercontent.com/26827453/179757487-e41cef79-0b8a-4ca0-9950-95e779e20c48.png)
![screencapture-192-168-32-1-index-html-2022-06-15-22_22_51](https://user-images.githubusercontent.com/26827453/179757489-ab4de259-7e80-4e4c-a712-653fd1d3cdfb.png)
![screencapture-192-168-32-1-index-html-2022-06-15-22_22_59](https://user-images.githubusercontent.com/26827453/179757491-ce9bd138-a2d9-4ae5-ab7a-e09090722be3.png)
![screencapture-192-168-32-1-index-html-2022-06-15-22_23_30](https://user-images.githubusercontent.com/26827453/179757494-dbe8fe67-57a2-43cc-b6aa-7e7a3672341e.png)
![screencapture-192-168-32-1-index-html-2022-06-15-22_24_08](https://user-images.githubusercontent.com/26827453/179757499-eab2119a-d3c5-4b6e-aed9-d4c31b7ef5d6.png)
![screencapture-192-168-32-1-index-html-2022-06-15-22_24_11](https://user-images.githubusercontent.com/26827453/179757502-f8f217cb-3ae6-4fbb-8b14-417915fc5078.png)
![screencapture-192-168-32-1-index-html-2022-06-16-02_29_25](https://user-images.githubusercontent.com/26827453/179757503-24de1d16-647c-4b4e-bdd6-7ba09b12a99c.png)
![screencapture-192-168-32-1-index-html-2022-06-16-07_13_48](https://user-images.githubusercontent.com/26827453/179757504-1ed8de71-9d63-4254-917f-1730b027e38d.png)
![screencapture-192-168-32-1-index-html-2022-06-16-07_15_49](https://user-images.githubusercontent.com/26827453/179757507-a68e8ffa-c2d0-44da-af25-502826b3bc79.png)
![screencapture-192-168-32-1-index-html-2022-06-16-07_19_17](https://user-images.githubusercontent.com/26827453/179757511-8e9f0285-d86b-4fa1-852b-98dc7f25e35b.png)
![screencapture-192-168-32-1-index-html-2022-06-16-07_35_43](https://user-images.githubusercontent.com/26827453/179757516-397414f7-66a5-45ae-bf67-57126bb05c26.png)
![screencapture-192-168-32-1-index-html-2022-06-16-08_02_25](https://user-images.githubusercontent.com/26827453/179757518-8ccd2a33-09bd-4c1a-923c-ab886b297038.png)
![screencapture-192-168-32-1-m-index-html-2022-06-16-02_59_19](https://user-images.githubusercontent.com/26827453/179757519-cb400b32-5ec5-4ba2-943a-dab3ec156bd7.png)
![screencapture-192-168-32-1-m-index-html-2022-06-16-07_37_40](https://user-images.githubusercontent.com/26827453/179757521-9b91da36-149e-4a30-810d-055ff2ec7c60.png)
![Screenshot_20220620_225407](https://user-images.githubusercontent.com/26827453/179757523-5c01a6ea-441d-4dc5-b128-4547907e9bce.png)



![Screenshot_20220620_054518](https://user-images.githubusercontent.com/26827453/175792863-8dadf776-7da2-4d5a-a553-7d5f9c8f8d38.png)
![Screenshot_20220620_055353](https://user-images.githubusercontent.com/26827453/175792869-aaa97b29-884e-4e83-83c6-8d17ad442b3b.png)



![20220609_133504](https://user-images.githubusercontent.com/26827453/175792888-a1a5e25f-49d6-4ac7-bc25-67cfcc97978d.jpg)
![20220609_133504(0)](https://user-images.githubusercontent.com/26827453/175792889-7f543583-f3f6-4c7b-a485-78081b824e52.jpg)
![20220609_133507](https://user-images.githubusercontent.com/26827453/175792890-a92ac80a-0a84-4b03-97d8-4aa22ed62bc2.jpg)
![20220609_133509](https://user-images.githubusercontent.com/26827453/175792892-d264499d-8963-4803-b61f-90d30b4bd399.jpg)
![20220609_133522](https://user-images.githubusercontent.com/26827453/175792893-b0e5c933-5723-4eb9-b4ee-673623c4e494.jpg)
![20220609_133523](https://user-images.githubusercontent.com/26827453/175792894-378710e7-61ea-489c-8ea2-1e0072527d50.jpg)
![20220609_133622](https://user-images.githubusercontent.com/26827453/175792896-053844eb-29c9-4797-b255-31b5462fdca3.jpg)
![20220609_133623](https://user-images.githubusercontent.com/26827453/175792897-26fe128e-96e2-413b-ab26-282b9cd853fb.jpg)
![20220609_133624](https://user-images.githubusercontent.com/26827453/175792900-d12c51c9-3810-4379-8859-59fc441f5558.jpg)
![20220609_133641](https://user-images.githubusercontent.com/26827453/175792901-9372122f-d6cd-461d-be70-ea8f491e29d4.jpg)
![20220609_133641(0)](https://user-images.githubusercontent.com/26827453/175792902-648c9740-c1ce-496f-8d2c-ecb7e7cb6abb.jpg)
![20220609_133646](https://user-images.githubusercontent.com/26827453/175792904-7ef3d037-6c3d-4e88-a62a-3d1fa61fb8c1.jpg)
![20220609_133647](https://user-images.githubusercontent.com/26827453/175792905-350d980c-2922-45ec-a7f9-94382b4ecfc2.jpg)
![20220609_133649](https://user-images.githubusercontent.com/26827453/175792906-aa176a44-830a-4496-a2c8-61d6b2ec2808.jpg)
![20220609_133651](https://user-images.githubusercontent.com/26827453/175792907-4da4f7c2-9633-4d91-8dc9-97852cd21fe3.jpg)
![20220609_134007](https://user-images.githubusercontent.com/26827453/175792908-3572fc3e-3d8a-435f-a54d-e589cece18fa.jpg)
![20220609_134008](https://user-images.githubusercontent.com/26827453/175792910-e478f5c7-2dcd-4883-a057-79cb6bd61899.jpg)
![20220609_134010](https://user-images.githubusercontent.com/26827453/175792911-ef8900bf-9475-480f-82f7-f07c009db841.jpg)
![20220611_040213](https://user-images.githubusercontent.com/26827453/175792912-1a76544d-dd39-4fc0-a0ce-2012593d31f2.jpg)
![20220611_040213(0)](https://user-images.githubusercontent.com/26827453/175792913-20892468-4018-40cb-ad45-b0f813f81819.jpg)
![20220611_040223](https://user-images.githubusercontent.com/26827453/175792914-ad9da62f-71ac-436b-b3f4-beab39e56bd6.jpg)
![20220611_040224](https://user-images.githubusercontent.com/26827453/175792915-60b098d4-4417-4765-befd-89a907ebc893.jpg)
![20220611_040227](https://user-images.githubusercontent.com/26827453/175792917-ce3bad9e-0be1-41ef-8fc1-f470b9468dfe.jpg)
![20220611_040227(0)](https://user-images.githubusercontent.com/26827453/175792918-d4f9969b-782f-4b38-bc30-ce9e6dfa8389.jpg)
![20220611_040251](https://user-images.githubusercontent.com/26827453/175792919-f7648823-6c36-4019-84f4-c853b742f3c3.jpg)
![20220611_040254](https://user-images.githubusercontent.com/26827453/175792920-052a54e4-97a2-47ce-90ed-c24501f4193b.jpg)
![20220611_040303](https://user-images.githubusercontent.com/26827453/175792921-48d3a4c4-7110-48e0-a10b-05bc93929b08.jpg)
![20220611_040313](https://user-images.githubusercontent.com/26827453/175792922-62e3f75d-8a34-4502-a06f-7161e5f209c2.jpg)
![20220611_040315](https://user-images.githubusercontent.com/26827453/175792923-d820361e-30a3-4166-9c3a-7f9c7c906dcc.jpg)
![20220611_040553](https://user-images.githubusercontent.com/26827453/175792924-f78736b7-9054-4557-a3e3-e584216a2120.jpg)
![20220611_040558](https://user-images.githubusercontent.com/26827453/175792926-44972af6-2ea1-4f85-8d63-f84922bc736e.jpg)
![20220611_040600](https://user-images.githubusercontent.com/26827453/175792927-c7a3a88a-2b85-4640-8384-c4e18e8b27c9.jpg)
![20220611_040601](https://user-images.githubusercontent.com/26827453/175792928-821b0f16-9f13-438d-8276-77071208dfd3.jpg)
![20220611_040616](https://user-images.githubusercontent.com/26827453/175792930-0a572cb8-d1c2-482b-8ecc-fc6f52e82732.jpg)
![20220611_040618](https://user-images.githubusercontent.com/26827453/175792931-1bb7923d-c76b-4e57-8dac-2ad1857aefeb.jpg)
![20220611_040620](https://user-images.githubusercontent.com/26827453/175792932-8c41a171-ee36-4270-97e8-0d90d52564ca.jpg)
![20220611_040725](https://user-images.githubusercontent.com/26827453/175792933-7a7f377c-f622-465c-b2de-23d80f60fa15.jpg)
![20220611_040734](https://user-images.githubusercontent.com/26827453/175792934-a2e341ac-d879-4cd3-9aee-30d09d6ce79b.jpg)
![20220611_040735](https://user-images.githubusercontent.com/26827453/175792935-e1b43ce5-df74-4ea1-b9ee-b923e2dafc2a.jpg)
![20220611_040737](https://user-images.githubusercontent.com/26827453/175792937-1abc0e2c-a15f-4cab-a051-72a664990da1.jpg)
![20220611_054016](https://user-images.githubusercontent.com/26827453/175792938-18cd253a-212b-408e-b833-1c8c6438e3ab.jpg)
