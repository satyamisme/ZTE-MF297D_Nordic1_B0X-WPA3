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

