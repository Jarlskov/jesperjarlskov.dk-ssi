---
title: 'PHP regular expression functions causing segmentation fault'
date: 2016-10-27
---

We recently had an issue where generating large exports for users to download would suddenly just stop for no apparent reason. Because of the size of the exports the first thought was that the process would time out, but the server didn't return a timeout error to the browser, and the process didn't really run for long enough to hit the time limit set on the server.

Looking through the Laravel and Apache vhost logs where the errors would normally be logged didn't provide any hints as to what the issue was. Nothing was logged. After some more digging I found out that I could provoke an error in the general Apache log file.

<pre>[core:notice] [pid 3639] AH00052: child pid 52372 exit signal Segmentation fault (11)</pre>

It wasn't a lot to go on, but at least I had a reproducible error message. A segmentation fault (or <a href="https://kb.iu.edu/d/aqsj">segfault</a>) means that a process tries to access a memory location that it isn't allowed to access, and for that reason it's killed by the operating system.

After following along the code to try to identify when the segfault actually happened, I concluded it was caused by a call too <a href="http://php.net/manual/en/function.preg-match.php"><code>preg_match()</code></a>.

Finally I had something concrete to start debugging (aka Googling) for, and I finally found out which <a href="http://stackoverflow.com/questions/7620910/regexp-in-preg-match-function-returning-browser-error">Stackoverflow question contained the answer</a>.

In short the problem happens because the <code>preg_*</code> functions in PHP builds upon the <a href="http://www.pcre.org/">PCRE library</a>. In PCRE certain regular expressions are matched by using a lot of recursive calls, which uses up a lot of stack space. It is possible to set a limit on the amount of recursions allowed, but in PHP this limit <a href="http://php.net/manual/en/pcre.configuration.php#ini.pcre.recursion-limit">defaults to 100.000</a> which is more than fits in the stack. Setting a more realistic limit for <code>pcre.recursion_limit</code> as suggested in the Stackoverflow thread solved my current issue, and if the limit should prove to be too low for my system's requirements, at least I will now get a proper error message to work from.

<a href="http://stackoverflow.com/questions/7620910/regexp-in-preg-match-function-returning-browser-error#answer-7627962">The Stackoverflow answer</a> contains a more in-depth about the problem, the solution and other related issues. Definitely worth a read.
