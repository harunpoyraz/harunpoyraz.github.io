---
layout: post
title: 'Diva Android Solutions '
description: DIVA Android - Damn Insecure and vulnerable App for Android
date: '2019-07-30 12:03:36 +0530'
categories: Solutions
published: true
---

## Diva Android Solutions

DIVA (Damn insecure and vulnerable App) is an App intentionally designed to be insecure. The aim of the App is to teach developers/QA/security professionals, flaws that are generally present in the Apps due poor or insecure coding practices.




![png]({{site.baseurl}}/pics/main.png)


## Insecure Logging

![png]({{site.baseurl}}/pics/insecure.png)

Develophers use logs to debug their applications. Leaking information in logs is general situation and can be found any system. In android, we can read logs from adb logcat command. Then when we look logs we see our application leakes our credit card number in logs.
 
 
```bash
adb logcat >  out.txt
cat out.txt | grep diva
```

![png]({{site.baseurl}}/pics/insecure-2.png)

![png]({{site.baseurl}}/pics/insecure1code.png)
When we look at the code the problematic part is that
  ```java 
Log.e("diva-log", "Error while processing transaction with credit card: " + localEditText.getText().toString());
```
which stores credit card number.


## Hardcoding Issues 1

![png]({{site.baseurl}}/pics/hardcodemain.png)

Sometimes develophers uses critical information as hard-coded. In these example hardcoded part in apk files.
To get apk  from our phone we use adb pull.
Then we use bytecodeviewer to open it. When we goto  related class HardcodeActivity we see blow result. Vendor secret leaked. 

![png]({{site.baseurl}}/pics/hardcode.png)

adb pull apk
bytecodeviever
vendorsecretkey


## Insecure Data Storage

![png]({{site.baseurl}}/pics/indata1main.png)

These time credentials stored in shared preferences which gives oppurtunity to store small information in xml files. It is stored in shared_prefs directory.
  ```bash 
adb shell
cd data/data/jakhar.aseem.diva/
ls
cd shared_prefs/
cat jakhar.aseem.diva_preferences.xml
```

![png]({{site.baseurl}}/pics/indata1code.png)

/data/data/jakhar.aseem.diva/shared_prefs

jakhar.aseem.diva_preferences.xml
```xml
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
	<map>
	    <string name="user">adadd</string>
	    <string name="password">sdsdsdsd</string>
	</map>
```



## Insecure Data Storage 2
![png]({{site.baseurl}}/pics/indata2main.png)

Common method to store data is using databases. In these example data  stored in sqlite databses plaintext.
To check database we download  by adb pull and open by sqlitebrowser.

```bash
adb pull data/data/jakhar.aseem.diva/databases/ids2

sqlitebrowser ids2
```
![png]({{site.baseurl}}/pics/indata2sli.png)

## Insecure Data Storage 3

![png]({{site.baseurl}}/pics/indata3main.png)

In these example, develpher tore data in tmp file which starts with uinfo.


```bash
adb pull /data/data/jakhar.aseem.diva/uinfo-493535252tmp
cat uinfo-493535252tmp
```
![png]({{site.baseurl}}/pics/indata3code.png)

## Insecure Data Storage 4
![png]({{site.baseurl}}/pics/indata4main.png)

These time data stored in different directory. To Read file we go location of $EXTERNAL_STORAGE variable.
```bash
echo $EXTERNAL_STORAGE
.uinfo.txt
```
![png]({{site.baseurl}}/pics/indata4code.png)

## Input Validation Issues 1
	
![png]({{site.baseurl}}/pics/input1main.png)	
	 	
When working with databases valitating input so critical. I fit not done properly or not used properly sql injecttion vulnerability occurs. Below input returns  alll results n table.

![png]({{site.baseurl}}/pics/input_valiatecode.png)

![png]({{site.baseurl}}/pics/inpur val1 resu.png)

```bash
	' or 1=1 --
```


## Input Validation Issues 2
![png]({{site.baseurl}}/pics/inputval2maiin.png)
Also file  inclusion vulnerabilities can be found  in mobile application. In example we can read local files by fiving them.
```bash
file:///data/data/jakhar.aseem.diva/lib/libdivajni.so
```

![png]({{site.baseurl}}/pics/inputval2res.png)

## Access Control Issues
![png]({{site.baseurl}}/pics/access1main.png)

Activities opened by intents and sometimes these activities are not secured. So they can can be called directly.

To do these we use drozer .To download and setup it you can use following link
https://github.com/mwrlabs/drozer

![png]({{site.baseurl}}/pics/acces1result.png)

```bash	
activity intent 
run app.activity.info  -a jakhar.aseem.diva
jakhar.aseem.diva.APICredsActivity
run app.activity.start --component jakhar.aseem.diva jakhar.aseem.diva.APICredsActivity
```


## Access Control Issues 3


![png]({{site.baseurl}}/pics/access3main.png)
Like activities, broviders can leake information. 
```bash	
run app.provider.info -a jakhar.aseem.diva
run scanner.provider.finduris -a jakhar.aseem.diva
run scanner.provider.sqltables -a content://jakhar.aseem.diva.provider.notesprovider/notes/
run app.provider.query content://jakhar.aseem.diva.provider.notesprovider/notes/
```
![png]({{site.baseurl}}/pics/acces3result.png)

## Hardcoding Issues 2

These time critical insormation is in libraries.

![png]({{site.baseurl}}/pics/hard2main.png)

```bash	
adb pull lib/lib.so
strings
olsdfgad;lh
```
![png]({{site.baseurl}}/pics/hard2code.png)
![png]({{site.baseurl}}/pics/hard2code2.png)

![png]({{site.baseurl}}/pics/hardcode2result.png)

## Input Validation Issues 3

![png]({{site.baseurl}}/pics/access3main.png)

Example includes overflow vulnerability.
To crach app we try each time more character.
When reached maximum app will crash.

```bash	
pppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppppp
```




