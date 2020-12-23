---
title: "Using Netcat For File Transfers"
date: 2020-06-19T15:12:24+07:00
description: Transferring files using netcat.
draft: false
hideToc: false
enableToc: false
enableTocContent: false
author: thik
authorEmoji: 🐱
#tags:
#- 
categories:
- Netcat 
#series:
#- 
image: images/feature/article-one.png
---

Netcat is like a swiss army knife for geeks. It can be used for just about anything involving TCP or UDP. One of its most practical uses is to transfer files. Non *nix people usually don't have SSH setup, and it is much faster to transfer stuff with netcat then setup SSH. netcat is just a single executable, and works across all platforms (Windows,Mac OS X, Linux).

On the receiving end running,

{{< highlight plain "linenos=table" >}}
nc -l -p 1234 > out.file
{{< / highlight >}}

will begin listening on port 1234.
On the sending end running,

{{< highlight plain "linenos=table" >}}
nc -w 3 [destination] 1234 < out.file
{{< / highlight >}}

will connect to the receiver and begin sending file.

For faster transfers if both sender and receiver has some basic *nix tools installed, you can compress the file during sending process,

On the receiving end,

{{< highlight plain "linenos=table" >}}
nc -l -p 1234 | uncompress -c | tar xvfp -
{{< / highlight >}}

On the receiving end,

{{< highlight plain "linenos=table" >}}
tar cfp - /some/dir | compress -c | nc -w 3 [destination] 1234
{{< / highlight >}}

A much cooler but less useful use of netcat is, it can transfer an image of the whole hard drive over the wire using a command called dd.

On the sender end run,

{{< highlight plain "linenos=table" >}}
dd if=/dev/hda3 | gzip -9 | nc -l 3333
{{< / highlight >}}

On the receiver end,

{{< highlight plain "linenos=table" >}}
nc [destination] 3333 | pv -b > hdImage.img.gz
{{< / highlight >}}

Be warned that file transfers using netcat are not encrypted, anyone on the network can grab what you are sending, so use this only on trusted networks.

#### Bonus

![Netcat Tutorial](/images/article/nc/netcat-cheat-sheet-i.jpg)
![Netcat Tutorial](/images/article/nc/netcat-cheat-sheet-ii.jpg)