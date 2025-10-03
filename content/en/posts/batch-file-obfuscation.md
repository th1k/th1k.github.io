---
title: "Best Way To Obfuscate a Batch File"
date: 2023-07-12T21:01:06+07:00
description: យល់ដឹងអំពីការធ្វើ Obfuscate ទៅលើឯកសារ Batch
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- fud
- obfuscate
categories:
- Evading
series:
- WinExE
image: images/feature3/terminal.gif
---

ខាងក្រោមនេះជាកូដសម្រាប់ធ្វើការ Obfuscate ទៅលើ Batch Script ដោយធ្វើការបំប្លែងកូដទាំងនោះទៅជាភាសារផ្សេងមួយទៀតដែលពិបាកយល់ ។

អរគុណចំពោះ [aGerman](https://www.dostips.com/forum/viewtopic.php?t=7990) សម្រាប់កូដមួយនេះ ។

#### PWN the Script

{{< highlight batch "linenos=table" >}}
@echo off
if "%~1"=="" exit /b
if /i "%~x1" neq ".bat" if /i "%~x1" neq ".cmd" exit /b
for /f %%i in ("certutil.exe") do if not exist "%%~$path:i" (
  echo CertUtil.exe not found.
  pause
  exit /b
)
>"temp.~b64" echo(//4mY2xzDQo=
certutil.exe -f -decode "temp.~b64" "%~n1___%~x1"
del "temp.~b64"
copy "%~n1___%~x1" /b + "%~1" /b
{{< / highlight >}}

#### Usage

ចម្លងកូដខាងលើហើយរក្សាទុកវាជា anyname.bat បន្ទាប់មកធ្វើការទំលាក់ឯកសារ Batch របស់អ្នកដែលចង់បំប្លែងនោះទៅលើឯកសារមួយនេះ នោះវានឹងធ្វើការបំប្លែងកូដដើមទៅជាកូដថ្មីមួយផ្សេងទៀតដោយខ្លួនឯង ។

#### Reverse Back

ដោយប្រើកូដខាងក្រោមនេះយើងអាចបំប្លែងកូដខាងលើដែលបំប្លែងរួច ឲ្យត្រលប់មកដើមវិញបាន៖

{{< highlight batch "linenos=table" >}}
@echo off
if "%~1"=="" exit /b
if /i "%~x1" neq ".bat" if /i "%~x1" neq ".cmd" exit /b
if exist "%~n1___%~x1" del "%~n1___%~x1"
for /f "skip=1 delims=" %%L in ('CMD /U /C Type "%~1"') do (
   echo %%L
   echo %%L >>"%~n1___%~x1"
)
pause>nul
{{< / highlight >}}

ឫក៏កូដខាងក្រោមប្រើបានដូចគ្នា

{{< highlight batch "linenos=table" >}}
@echo off &setlocal
if "%~1"=="" exit /b
if /i "%~x1" neq ".bat" if /i "%~x1" neq ".cmd" exit /b
<"%~1" ((for /l %%N in (1 1 8) do pause)>nul&findstr "^">"%~n1___%~x1")
{{< / highlight >}}

#### Usage

ដូចគ្នានេះដែរគឺគ្រាន់តែរក្សាវាទុកជា *.bat រួចហើយទំលាក់កូដដែលបានបំប្លែងរួចទៅលើវា នោះវានឹងបំប្លែងកូដដើមចេញមកវិញ ៕