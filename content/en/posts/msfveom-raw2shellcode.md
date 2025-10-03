---
title: "MSFvenom:- Raw2Shellcode and Remove Garbage"
date: 2023-08-09T21:01:06+07:00
description: យល់ដឹងអំពីការបំប្លែងឯកសារ Raw ទៅជា Shellcode និងដកចំនួនមិនបានការចោល
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
- MSFvenom
series:
- WinExE
image: images/feature3/terminal.gif
---

ក្នុងអត្ថបទនេះយើងនឹងមកដឹងអំពីវិធីបំប្លែង​ឯកសារ Payload ឫ Beacons ផ្សេងៗដែលជាឯកសារប្រភេទ Raw ឲ្យទៅជា Shellcode ដើម្បីដាក់បញ្ចូលទៅក្នុងភាសារ C ក្នុងការបង្កើតជា Custom Payloads ជំនួសវិញ ។

#### Shellcode2Raw
ខាងក្រោមនេះគឺជាគន្លឹះក្នុងការបំប្លែង Shellcode ទៅជាឯកសារ Raw ។

{{< highlight zsh "linenos=table" >}}
echo -ne "\x68\x62\x69\x3a\x00\x68[...]\x4b\x65\x6e\x6f\x68\x72" > raw.bin
{{< / highlight >}}

#### Raw2Shellcode
ខាងក្រោមនេះគឺជាគន្លឹះក្នុងការបំប្លែងឯកសារ Raw ទៅជា Shellcode វិញ ។

{{< highlight zsh "linenos=table" >}}
cat raw.bin | msfvenom -p - -a x64 --platform win -e x86/shikata_ga_nai -f c
{{< / highlight >}}

![Raw2Shellcode](/en/posts/img/2023/08/raw2shellcode/raw2shellcode-1.png)

ពន្យល់អំពី Parameters:-
- -p - ប្រាប់ទៅ msfvenom ឲ្យអានប្រភេទនៃ​ payload របស់ឯកសារចេញពី stdin ដែលបានមកពីការ cat raw.bin
- -a កំណត់ប្រភេទនៃ architecture (x64/x86)
- --platform កំណត់ប្រភេទនែប្រព័ន្ធប្រតិបត្តិការ
- -e កំណត់ encoder របស់ payload (រំលងបាន)
- -f កំណត់ប្រភេទនៃ payload ដែលចង់បាន

#### Raw2Shellcode / Remove Garbage
ខាងក្រោមនេះគឺជាគន្លឹះក្នុងការបំប្លែងឯកសារ Raw ទៅជា Shellcode វិញនិងផ្សំជាមួយការដកចេញនៅរាល់ចំនួនមិនបានការដូចជា \x00 ជាដើម ។

{{< highlight zsh "linenos=table" >}}
cat raw.bin | msfvenom -p - -a x64 --platform win -e x86/shikata_ga_nai -f c -b '\x00'
{{< / highlight >}}

ពន្យល់អំពី Parameter:-
- -b បន្ថែម syntax មួយនេះដើម្បីដកនូវរាល់ចំនួនអក្សរឫលេខដែលមិនបានការចេញ

ជាលទ្ធផលយើងនឹងទទួលបានចំនួន Shellcode ពិតដែលមិនមានលាយឡំជាមួយនិងចំនួនអក្សរឫលេខដែលមិនបានការ​ ៕

<b>សម្រង់អត្ថបទ៖</b> [ivanitlearning](https://ivanitlearning.wordpress.com/2018/10/14/shellcoding-with-msfvenom/)