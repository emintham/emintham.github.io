---
layout: post
title:  "A simple exercise in deobfuscation (gogo2me script)"
date:   2014-05-03 13:27:00
categories: [RE, JS, Malware]
tags: [gogo2me, RE, JS, deobfuscation]
---

Script Source: [@MalwareMustDie][twitter]

For the impatient: @MalwareMustDie's [writeup][pastebin]

In this article, we will go over the steps to deobfuscate the following 
script:
{% gist emintham/afc9916b9108a6cb1ee4 %}

First, we will try to indent it in a more human readable format to better 
understand the structure of the code. Many IDEs support auto-indentation
but, for this walkthrough, we will use a free online-tool called 
[jsbeautifier][beautifier] to do so. The beautified code is as follows:
{% gist emintham/0f12620e047d85c37296 %}

We notice that the function names and arguments have been replaced with 
random characters so we start by making it more human-readable by simple
substitution. 

Next, we notice that there are two nested functions that simply return a number. We can then replace the calls to those functions by the numbers
themselves:

{% gist emintham/4528ad725aa08e15e8ab %}

Notice also that there are two variables assigned the value of the empty 
string-- `l4965411b24158` and `x11`. In the first case, it is assigned a
new value and in the second, it is used in constructing a new string. 
We can remove all `x11`s from the large string construction. Further, 
notice that `l4965411b250fb` is assigned as an alias of 
`String.fromCharCode`. Substituting all the relevant changes, we get that:
{% gist emintham/87470887011f821d9961 %}

Here, we see that the exploit string is encoded as the hexadecimal of the
character codes of the actual exploit string and `func2` decodes it. 
Instead of using `document.write`, we'll just print out the string to 
examine its behaviour. Running it in a REPL, eg. [repl.it][repl], yields:
{% gist emintham/fa2f4704c73fe7eb5aba %}

We can then check just the `unescaped` portion of the script, taking care
to remove the backslashes `\`. This gives us the following iframe:
{% gist emintham/833bcbfec905f7584a14 %}

[twitter]: https://twitter.com/malwaremustdie/status/462549522313277441
[pastebin]: http://pastebin.com/MWbxetKN
[beautifier]: http://jsbeautifier.org/
[repl]: http://repl.it
