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

### Login on router, no other methods works and the ones around the web is old, do as below for login:

### Generate LD Token

```sh
LD="$(curl 'http://192.168.32.1/goform/goform_get_cmd_process?isTest=false&cmd=LD&_=1655363462531' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: sv-SE,sv;q=0.9,en-US;q=0.8,en;q=0.7' \
  -H 'Connection: keep-alive' \
  -H 'Referer: http://192.168.32.1/' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --compressed \
  --insecure|cut -d'"' -f4)"
```

### Login

{"result":"1"} = `success`

{"result":"3"} = `invalid`

```sh
  curl 'http://192.168.32.1/goform/goform_set_cmd_process' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: sv-SE,sv;q=0.9,en-US;q=0.8,en;q=0.7' \
  -H 'Connection: keep-alive' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  -H 'Origin: http://192.168.32.1' \
  -H 'Referer: http://192.168.32.1/' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw "isTest=false&goformId=LOGIN&password=$(LD)" \
  --compressed \
  --insecure
```

### Disable tr69

To avoid to upgrade to the newer version, turn off tr069 or edit the ACSURL (wich is hidden and disabled for us but they forgot to disable it)

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

### CVE reports by others

| Date | Current Version    | Affected Version                            |  Fixed Version                          |
| :----------------- | :----------------- | :-------------------------------- | :----------------------------------|  
| 2022-06-09 | `MF297D`            |  MF297D_Nordic1_B05    | MF297D_Nordic1_B06                               |

ZTE's MF297D product has cryptographic issues vulnerability. 
Due to the use of weak random values, the security of the device is reduced, and it may face the risk of attack. 

https://www.cve.org/CVERecord?id=CVE-2022-23138

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


![20220609_121737](https://user-images.githubusercontent.com/26827453/175792885-6688b3dc-0870-4cbf-b8d5-7054bd6f02d2.jpg)
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
![20220611_054018](https://user-images.githubusercontent.com/26827453/175792939-1267c621-3c4e-4d11-8d5c-857307f14d2f.jpg)
![20220611_054359](https://user-images.githubusercontent.com/26827453/175792940-6aa5a675-60ed-474e-a130-ba1f50da3e8b.jpg)
![20220611_054402](https://user-images.githubusercontent.com/26827453/175792941-4b752a9b-cb04-4067-a68c-2de9fd1c1a35.jpg)
![20220611_091741](https://user-images.githubusercontent.com/26827453/175792942-a1bae7ea-2791-41ee-bff5-b644b8fe32f7.jpg)
![20220611_091745](https://user-images.githubusercontent.com/26827453/175792943-01ac78a9-20f4-446b-b906-be73d9eb3272.jpg)
![20220611_091901](https://user-images.githubusercontent.com/26827453/175792944-fd21841a-4c60-4e61-8e5d-7fb60d1b8e2c.jpg)
![20220611_091903](https://user-images.githubusercontent.com/26827453/175792946-ef4e1343-348d-4575-8e10-026dbdec04e3.jpg)
![20220611_091904](https://user-images.githubusercontent.com/26827453/175792947-cdbfe125-652b-47fe-90bf-f963d15005c6.jpg)
![20220611_091906](https://user-images.githubusercontent.com/26827453/175792948-458488c2-4929-4fd1-b8e5-347666a23e5c.jpg)
![20220611_091909](https://user-images.githubusercontent.com/26827453/175792949-0652b82b-fc50-42cc-a52b-3cb6869e7693.jpg)
![20220611_092147](https://user-images.githubusercontent.com/26827453/175792951-a9efc611-0b11-47b5-bdf1-739fefc88bf3.jpg)
![20220611_092155](https://user-images.githubusercontent.com/26827453/175792952-839bcdc7-27af-489e-9202-3c006a748f19.jpg)
![20220611_092157](https://user-images.githubusercontent.com/26827453/175792953-3bcc11fe-beb7-4d09-99d9-43c4c1d8105f.jpg)
![20220611_092200](https://user-images.githubusercontent.com/26827453/175792955-c241bbf2-7344-48f1-a31a-c7676bb847a1.jpg)
![20220611_092322](https://user-images.githubusercontent.com/26827453/175792956-2df02c93-d2f6-4702-821c-c9502d47dae1.jpg)
![20220611_092324](https://user-images.githubusercontent.com/26827453/175792957-9561d9ce-9d02-4596-bf2c-10aa017a0db8.jpg)
![20220611_092332](https://user-images.githubusercontent.com/26827453/175792958-fad82369-d811-4bc9-bccd-e22c5f9f7e3b.jpg)
![20220611_092334](https://user-images.githubusercontent.com/26827453/175792959-a65f1197-410d-4ba9-9d6a-24316b59125a.jpg)
![20220611_092338](https://user-images.githubusercontent.com/26827453/175792960-7313ef15-c889-4e49-b0e1-b6fd44af512d.jpg)
![20220611_092340](https://user-images.githubusercontent.com/26827453/175792961-f96041d3-0ca5-473a-958c-964389eba0fe.jpg)
![20220611_092345](https://user-images.githubusercontent.com/26827453/175792962-27660394-333a-486e-8c3a-1acc60de0cee.jpg)
![20220611_092352](https://user-images.githubusercontent.com/26827453/175792963-b7b9b877-1290-4bf2-b0ce-eb0db6de1460.jpg)
![20220611_092359](https://user-images.githubusercontent.com/26827453/175792964-325071af-3841-40b5-98e8-c9395d3983ad.jpg)
![20220611_092503](https://user-images.githubusercontent.com/26827453/175792965-24ebe462-9ae2-4f9f-b27d-b86730d09ca5.jpg)
![20220611_092506](https://user-images.githubusercontent.com/26827453/175792966-ad2b5a00-756a-4675-929a-adbaa896b45f.jpg)
![20220611_092508](https://user-images.githubusercontent.com/26827453/175792967-ca4eecb1-6c5c-4e18-97a8-6f49ac47caff.jpg)
![20220611_092510](https://user-images.githubusercontent.com/26827453/175792968-ec7b6baf-d56a-43bb-8633-c61fa27c8e10.jpg)
![20220611_092658](https://user-images.githubusercontent.com/26827453/175792969-40d486ef-1968-4754-919e-36ab001e3ee2.jpg)
![20220611_092847](https://user-images.githubusercontent.com/26827453/175792971-a1980019-90b7-4594-a340-f0171d8fee63.jpg)
![20220611_093446](https://user-images.githubusercontent.com/26827453/175792973-b3cc7873-1d4b-408b-9b7b-847a6c4d5ac4.jpg)
![20220611_093514](https://user-images.githubusercontent.com/26827453/175792975-220b27ea-a445-4223-853d-abc2daf6af32.jpg)
![20220611_093524](https://user-images.githubusercontent.com/26827453/175792976-540f94fc-d979-498b-9048-43e3fb76511f.jpg)
![20220611_093526](https://user-images.githubusercontent.com/26827453/175792977-30936e5f-b028-4ca6-9f8c-b973de02ae44.jpg)
![20220611_093533](https://user-images.githubusercontent.com/26827453/175792978-bf5d107e-3bd2-4542-99af-cb83bf8359b4.jpg)
![20220611_093539](https://user-images.githubusercontent.com/26827453/175792979-251f8ece-c14b-4936-a07b-d85656845f70.jpg)
![20220611_093540](https://user-images.githubusercontent.com/26827453/175792981-02ad7e43-dcb3-4cae-9d4b-2ddd278e82a1.jpg)
![20220611_093543](https://user-images.githubusercontent.com/26827453/175792982-48688203-80a0-441d-8079-6e0c7b3894c7.jpg)
![20220611_093651](https://user-images.githubusercontent.com/26827453/175792983-8e615b75-cab2-4e13-9846-7b7a2fbfe5ca.jpg)
![20220611_093654](https://user-images.githubusercontent.com/26827453/175792984-ca1b53b1-1e2d-483f-894c-2e9e70ea8fd9.jpg)
![20220611_093656](https://user-images.githubusercontent.com/26827453/175792985-4e469dd7-58ab-4941-b185-38a0b2a8ed12.jpg)

