---
title: "Self Signed Certification"
date: 2020-06-02T16:22:34+07:00
description: របៀបបង្កើត Certification សម្រាប់ Code Signing
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags:
- code-signing
categories:
- CA
series:
- WinExE
image: images/feature3/ca.png
---

តើមានវិធីអ្វីដែលអាចធ្វើ Code Signing ទៅលើ Payload ដែលយើងបានបង្កើតឡើងដោយខ្លួនឯងរួច?

ខាងក្រោមនេះគឺជាវិធីដ៏ល្អមួយសម្រាប់ចម្លើយនៃសំនួរខាងលើ៖

#### Generate the Key

{{< highlight powershell "linenos=table" >}}
# Make sure you run as Administrator
New-SelfSignedCertificate -DnsName email@yourdomain.com -Type CodeSigning -CertStoreLocation cert:\CurrentUser\My
{{< / highlight >}}

#### Export Certification Without Private Key

{{< highlight powershell "linenos=table" >}}
Export-Certificate -Cert (Get-ChildItem Cert:\CurrentUser\My -CodeSigningCert)[0] -FilePath code_signing.crt
{{< / highlight >}}

ចំពោះលេខ [0] គឺសម្រាប់ករណីនៅពេលដែលយើងមាន Certificate ច្រើនជាងមួយ។ 

#### Import as Trusted Publisher 

{{< highlight powershell "linenos=table" >}}
Import-Certificate -FilePath .\code_signing.crt -Cert Cert:\CurrentUser\TrustedPublisher
{{< / highlight >}}

#### Import as Root Certificate Authority

{{< highlight powershell "linenos=table" >}}
Import-Certificate -FilePath .\code_signing.crt -Cert Cert:\CurrentUser\Root
{{< / highlight >}}

#### Signing Application

{{< highlight powershell "linenos=table" >}}
Set-AuthenticodeSignature .\app.exe -Certificate (Get-ChildItem Cert:\CurrentUser\My -CodeSigningCert)
{{< / highlight >}}


គួរចំណាំផងដែរថា នៅពេលដែលយើងបានដំឡើងសោរនេះរួចរាល់ហើយ យើងក៏អាចចុះហត្ថលេខា ទៅឲ្យស្គ្រីបផ្សេងទៀតជាមួយវាបានផងដែរ។

<b>ប្រភព៖</b> [stackoverflow](https://stackoverflow.com/questions/84847/how-do-i-create-a-self-signed-certificate-for-code-signing-on-windows)
<b>អត្ថបទទាក់ទង៖</b> [sid-500.com](https://sid-500.com/2017/10/26/how-to-digitally-sign-powershell-scripts/)
