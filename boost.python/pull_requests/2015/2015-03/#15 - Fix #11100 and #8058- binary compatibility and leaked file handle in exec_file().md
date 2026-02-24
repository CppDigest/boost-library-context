# #15 Fix #11100 and #8058: binary compatibility and leaked file handle in exec_file() [Merged]

> Username: mmatrosov  
> Created at: 2015-03-10 22:23:44 UTC  
> Updated at: 2015-09-09 15:20:24 UTC  
> Merged at: 2015-03-26 12:23:15 UTC  
> Closed at: 2015-03-26 12:23:15 UTC  
> Url: https://github.com/boostorg/python/pull/15  

Fixes the following bugs:  
https://svn.boost.org/trac/boost/ticket/11100  
https://svn.boost.org/trac/boost/ticket/8058

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2015-03-26 12:23:07 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-86487807  

Thanks for the patches, these look good.

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2015-06-30 15:54:46 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117236866  

The comments appear contradictory: https://svn.boost.org/trac/boost/ticket/8058 reports the issue of non-closed file handles specifically with Python 2.7 on a 64-bit host, while comments above suggest that it's only with Python 2.7 that the file is (attempted to be) closed twice.

---

## Comment 3

> Username: mmatrosov  
> Created_at: 2015-06-30 16:37:31 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117248477  

@stefanseefeld I encountered the problem described in this ticket under Python 3.4 (32 bit), and tested the fix only under this version. I suggested that this will also fix it under other versions and platforms and I hoped someone will test it. It sounds odd indeed that @reven86 says that behaviour under Python 2.7 is different then described in the ticket.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2015-06-30 16:41:02 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117249791  

@mmatrosov , thanks. For avoidance of doubt: by "the problem" are you referring to the dangling open file descriptor, or to the double free ? I wonder what may cause the file not to be closed via the handle<> destructor to begin with, as that's the typical fail-safe (and exception-safe !) technique used throughout C++.

---

## Comment 5

> Username: mmatrosov  
> Created_at: 2015-06-30 17:13:18 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117262350  

@stefanseefeld By "the problem" I am referring to the dangling open file descriptor, that's why I added `closeit` flag in the first place. From the interface I saw no reason why file descriptor should outlive the exec function, that's why I decided to close it immediately. Yes, it was unclear for me, why it was not closing in handle's destructor. But I was not thorough in that fix, perhaps handle's destructor is the right place to close file descriptor in this case. Feel free to implement the solution you find more appropriate.

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2015-06-30 18:09:26 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117287524  

@mmatrosov Thanks for the confirmation. Did you, as the OP, observe the open file descriptors accumulating when running program from https://svn.boost.org/trac/boost/ticket/8058 ? Or just after the "exec_file" function (were the file should indeed be still open) ?  
Just trying to attempt to reproduce the original failure...  
I think I'm going to revert this patch for the upcoming release, even if I can't find a proper fix.

---

## Comment 7

> Username: mmatrosov  
> Created_at: 2015-07-01 08:07:21 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117519623  

@stefanseefeld I'm sorry, I do not see the difference between the two ("open file descriptors accumulating when running program from ticket 8058" and "just after the "exec_file" function"). What do you mean? Once `exec_file` is finished, returned handle should be destroyed (unless stored in a variable) and file descriptor should be closed anyway.

---

## Comment 8

> Username: stefanseefeld  
> Created_at: 2015-07-01 11:41:11 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117624883  

@mmatrosov Sorry, I meant to ask whether you looked at the file after the execution of PyRun_File(), or after exec_file(). But I assume it's the latter.  
In any case, the patch is reverted, and I'm waiting to see a report of the file still being open after the handle<> object is destroyed (as the OP seemed to imply). I still don't see how that could ever happen...

---

## Comment 9

> Username: mmatrosov  
> Created_at: 2015-07-01 12:54:54 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117650909  

@stefanseefeld Interesting. I may prepare a reprocase and send it to you. I used MS Visual Studio 2013, Python 3.4.3, boost 1.57, Windows 7 x64, 32-bit build.

---

## Comment 10

> Username: stefanseefeld  
> Created_at: 2015-07-01 12:59:39 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117653117  

OK. Please be aware that I don't have Windows here (much less MSVC), so I'm relying on other people testing and debugging on such platforms. So if you could step through the debugger and analyze why the file isn't getting closed, that would be very much appreciated. Thanks !

---

## Comment 11

> Username: mmatrosov  
> Created_at: 2015-07-01 13:05:20 UTC  
> Updated_at: 2015-07-01 13:05:33 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117657434  

@stefanseefeld   
  
> Please be aware that I don't have Windows here (much less MSVC)  
  
How is boost::python tested on other platforms?

---

## Comment 12

> Username: stefanseefeld  
> Created_at: 2015-07-01 14:08:58 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117688240  

Different testers use different test platforms, so as long as all supported platforms are covered by some testers, we are fine. Of course, to be able to develop for a specific platform, the developer needs to have access to such a platform. I suppose I could set up a VM with Windows / MSVC on it, but I haven't worked with that platform for ~10 years, so I'd rather have someone else take over that responsibility.

---

## Comment 13

> Username: mmatrosov  
> Created_at: 2015-07-01 14:38:54 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117700677  

@stefanseefeld Is new functionality tested when it is already merged in master? If there are any specifications of this process (development, merging, testing) specific to boost::python, could you please give me a link to those?

---

## Comment 14

> Username: stefanseefeld  
> Created_at: 2015-07-01 14:56:46 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-117707370  

I'm not aware of any formal documentation of the process (the boost  
online docs are quite messy as far as that is concerned, as they still  
refer to svn in some places).  
  
There are test runs for both master and the "develop" branch, so the  
idea is that all development happens on the "develop" branch, and get  
merged to "master" only once things are stable enough. I merged  
"develop' to "master" last night after comparing the state of both  
(including test results).  
  
http://www.boost.org/development/tests/develop/developer/python.html  
http://www.boost.org/development/tests/master/developer/python.html  
  
are the two test reports.  
  
##   
  
```  
  ...ich hab' noch einen Koffer in Berlin...  
```

---

## Comment 15

> Username: mmatrosov  
> Created_at: 2015-08-22 14:09:40 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-133707326  

Hi, @stefanseefeld. I'm currently investigating the issue. It doesn't seem complicated: we just need to destroy file handle either with help of `python::handle<>`, or with `closeIt` flag.   
  
However, now I cannot even make `exec_file` work under Python 3.4 (everything is ok for Python 2.7). I'm trying to call it like in function `exec_file_test()` from `boost.python\test\exec.cpp`:  
  
```  
boost::python::dict global;  
exec_file("script.py", global, global);  
```  
  
And for this `PyRun_FileEx` inside `exec_file` returns `null`. Is this a bug? Can you advise me on what to do?

---

## Comment 16

> Username: bchretien  
> Created_at: 2015-09-09 07:07:48 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-138809077  

Hi, I am also observing this bug with Boost 1.58 and Python 2.7 on Arch Linux (in case you need an extra tester for a fix). Any update on the solution?

---

## Comment 17

> Username: stefanseefeld  
> Created_at: 2015-09-09 11:42:09 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-138884877  

I'm not sure what you are referring to as "this bug" here. The one I'm aware of is the leaked file descriptor. For that it would be most useful to come up with a minimal test case, so someone can walk through it in a debugger on one of those platforms that show this behavior, and identify the cause.

---

## Comment 18

> Username: bchretien  
> Created_at: 2015-09-09 14:06:04 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-138919577  

@stefanseefeld I am referring to the bug detailed in the comments of fe24ab9dd5440562e27422cd38f7de03356bfd16, but maybe this has its own issue on GH and/or was already fixed. The repro code simply involves calling `exec_file` with Python 2.7 (cf. the example showed [here](https://github.com/s-nakaoka/choreonoid/issues/33)).

---

## Comment 19

> Username: stefanseefeld  
> Created_at: 2015-09-09 14:16:36 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-138923297  

That is fixed in 1.59.

---

## Comment 20

> Username: bchretien  
> Created_at: 2015-09-09 15:20:24 UTC  
> Url: https://github.com/boostorg/python/pull/15#issuecomment-138944534  

@stefanseefeld: wonderful, thanks!

---
