---
title: "Self Signed Certification for Code Signing"
date: 2020-06-02T16:22:34+07:00
description: Creating certification for code signing on windows 10.
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: thik
authorEmoji: 🐱
#tags:
#- 
categories:
- CA
series:
- WinExE
image: images/feature/ca.png
---

In order to use a non deprecated way to sign your own script, one should use [New-SelfSignedCertificate](https://docs.microsoft.com/en-us/powershell/module/pkiclient/new-selfsignedcertificate?view=win10-ps).

#### Generate the Key

{{< highlight powershell "linenos=table" >}}
# Make sure you run as Administrator
New-SelfSignedCertificate -DnsName email@yourdomain.com -Type CodeSigning -CertStoreLocation cert:\CurrentUser\My
{{< / highlight >}}

#### Export Certification Without Private Key

{{< highlight powershell "linenos=table" >}}
Export-Certificate -Cert (Get-ChildItem Cert:\CurrentUser\My -CodeSigningCert)[0] -FilePath code_signing.crt
{{< / highlight >}}

The [0] will make this work for cases when you have more than one certificate. Obviously make the index match the certificate you want to use or use a way to filtrate (by thumprint or issuer).

#### Import as Trusted Publisher 

{{< highlight powershell "linenos=table" >}}
Import-Certificate -FilePath .\code_signing.crt -Cert Cert:\CurrentUser\TrustedPublisher
{{< / highlight >}}

#### Import as Root Certificate Authority

{{< highlight powershell "linenos=table" >}}
Import-Certificate -FilePath .\code_signing.crt -Cert Cert:\CurrentUser\Root
{{< / highlight >}}

Item Cert:\C
#### Signing Application

{{< highlight powershell "linenos=table" >}}
Set-AuthenticodeSignature .\app.exe -Certificate (Get-ChildItem Cert:\CurrentUser\My -CodeSigningCert)
{{< / highlight >}}

Obviously once you have setup the key, you can simply sign any other scripts with it.

<b>Source:</b> [stackoverflow](https://stackoverflow.com/questions/84847/how-do-i-create-a-self-signed-certificate-for-code-signing-on-windows)
