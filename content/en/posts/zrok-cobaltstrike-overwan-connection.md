---
title: "[Zrok] - CobaltStrike Over WAN Connection"
date: 2024-06-16T13:00:00+07:00
description: តភ្ជាប់និងប្រើប្រាស់ពាក្យបញ្ជាក្នុង CobaltStrike ពីចំងាយ
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- tunnel
- over-wan
categories:
- C2
series:
- CobaltStrike
image: images/feature3/cobaltstrike.jpg
---

ខាងក្រោមនេះជាគំនិតផ្ទាល់ខ្លួនមួយប្រភេទផ្សេងទៀតក្នុងការតភ្ជាប់ Teamserver និងប្រើប្រាស់ Beacons ពីចំងាយតាមរយៈជំនួយពី Zrok ។

#### Requirements

* CobaltStrike
* Email
* Zrok
* Telebit

#### Zrok Setup

ចូលទៅតំណភ្ជាប់នេះ https://docs.zrok.io/docs/getting-started/ បន្ទាប់មកសូមទាញយកកម្មវិធីឲ្យត្រូវទៅតាមប្រភេទនៃម៉ាស៊ីនមេរបស់អ្នក ក្នុងនៃនេះខ្ញុំនឹងយកប្រភេទ Linux ធ្វើជាម៉ាស៊ីនមេ ។

{{< highlight zsh "linenos=table" >}}
wget https://github.com/openziti/zrok/releases/download/v0.4.31/zrok_0.4.31_linux_arm64.tar.gz -O zrok.tar.gz
{{< / highlight >}}

ពន្លាឯកសារចេញនោះអ្នកនឹងទទួលបាន​ឯកសារគោលគឺ Zrok ។

ចូលទៅ Terminal និងវាយពាក្យបញ្ជាដូចខាងក្រោម៖

{{< highlight zsh "linenos=table" >}}
./zrok invite
{{< / highlight >}}

បន្ទាប់មកសូមបញ្ជូល Email របស់អ្នកដើម្បីចុះឈ្មោះប្រើប្រាស់សេវាកម្ម ។

![Zrok Invite](/en/posts/img/2024/06/zrok-cbs-overwan/1-invite.jpg)

![Confirm Email](/en/posts/img/2024/06/zrok-cbs-overwan/2-confirm-email.jpg)

សូមចូលទៅ Activate គណនី Zrok តាមរយៈ Email របស់អ្នក ។

នៅក្នុងសារ Email របស់អ្នកនឹងទទួលបានរូបដូចខាងក្រោម សូមចុចពាក្យថា Create Account ។

![Create Account](/en/posts/img/2024/06/zrok-cbs-overwan/3-create-account.jpg)

សូមបំពេញលេខសំងាត់ ។

![Create Password](/en/posts/img/2024/06/zrok-cbs-overwan/4-pwd.jpg)

ការបង្កើតបានជោគជ័យ ។

![Enable Account](/en/posts/img/2024/06/zrok-cbs-overwan/5-enable.jpg)

ដើម្បី Enable ម៉ាស៊ីនឲ្យស្គាល់និងអាចប្រើប្រាស់សេវាកម្ម ​Zrok បាន សូមចម្លងពាក្យបញ្ជាខាងក្រោមដាក់ចូលទៅក្នុងម៉ាស៊ីនមេរបស់អ្នក ។

{{< highlight zsh "linenos=table" >}}
./zrok enable xxxxxxx
{{< / highlight >}}

![Enable Account](/en/posts/img/2024/06/zrok-cbs-overwan/6-linux-enable.jpg)

ដើម្បីបើកមើល Realtime Connection រវាងម៉ាស៊ីនមេ កូនម៉ាស៊ីន ឬ ផ្នែកផ្សេងទៀតនៃការវាយប្រហារ សូមចូលទៅកាន់ Zrok Web API ដើម្បីបានព័ត៌មានជាក់លាក់ ។

តំណរភ្ជាប់៖ [api.zrok](https://api.zrok.io/)

បំពេញឈ្មោះ និង លេខសំងាត់របស់ដើម្បីចូល ។

![Zrok Web UI](/en/posts/img/2024/06/zrok-cbs-overwan/7-api-zrok.jpg)

#### Setup Teamserver Session

ចូលទៅកាន់ម៉ាស៊ីនមេរបស់អ្នករួចបើក CBS Server ។

![Start CBS Server](/en/posts/img/2024/06/zrok-cbs-overwan/8-cbs-start-server.jpg)

បើក Terminal មួយទៀតនិងពាក្យបញ្ជាដូចខាងក្រោម៖

{{< highlight zsh "linenos=table" >}}
//ខាងក្រោមជាការ Share តាមបែប TCP
./zrok share private --backend-mode tcpTunnel 192.168.200.151:50050
{{< / highlight >}}

![Share Private TCP Session](/en/posts/img/2024/06/zrok-cbs-overwan/9-share-tcp-session.jpg)

រួចរាល់ក្នុងការ Sync Session ពី Zrok និង CBS Server ។

#### Connect Teamserver

ដើម្បីតភ្ជាប់ទៅកាន់ Teamserver បានយើងត្រូវតភ្ជាប់ Private Session ទៅកាន់ Zrok Account ជាមុនសិន សូមវាយពាក្យបញ្ជាដូចខាងក្រោម៖

{{< highlight zsh "linenos=table" >}}
//Session យកចេញពីការភ្ជាប់ខាងលើ
./zrok access private xxxxxxxx
{{< / highlight >}}

![Connect Private TCP Session](/en/posts/img/2024/06/zrok-cbs-overwan/10-private-connect-ts.jpg)

រួចរាល់ បន្ទាប់មកដើម្បីតភ្ជាប់ទៅកាន់ CBS Server សូមបំពេញដូចខាងក្រោម៖

![Connecting CBS Client](/en/posts/img/2024/06/zrok-cbs-overwan/11-cbs-client-connect.jpg)

![CBS Client Connected](/en/posts/img/2024/06/zrok-cbs-overwan/12-cbs-connected.jpg)

ជោគជ័យ ។

ចំពោះ Listener និង Beacons សូមចូលទៅកាន់[អត្ថបទមុន](https://th1k.github.io/en/posts/cobaltstrike-overwan-connection/)របស់ខ្ញុំ ដែលប្រើប្រាស់ Telebit ក្នុងការបង្កើត ៕