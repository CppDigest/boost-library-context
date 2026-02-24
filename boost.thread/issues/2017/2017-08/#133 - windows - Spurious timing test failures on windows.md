# #133 - windows - Spurious timing test failures on windows. [Closed]

> Username: garyfurnish  
> Created at: 2017-08-27 23:37:51 UTC  
> Updated at: 2017-10-15 19:21:33 UTC  
> Closed at: 2017-08-28 14:35:36 UTC  
> Url: https://github.com/boostorg/thread/issues/133  

Related to [Trac 9856](https://svn.boost.org/trac10/ticket/9856) in test_9856  
  
> The fact that Thread doesn't return timeout up to 10% of the time on Windows is wontfix.  
  
Can we at least change the threshold for the test on windows so that we don't get spurious failures?    
  
This was tested in 3222938.

---

## Comment 1

> Username: viboes  
> Created at: 2017-08-28 04:43:58 UTC  
> Url: https://github.com/boostorg/thread/issues/133#issuecomment-325258902  

Could you tell me on which platform and compiler you got the issue? are those run in http://www.boost.org/development/tests/develop/developer/thread.html?

---

## Comment 2

> Username: garyfurnish  
> Created at: 2017-08-28 05:19:40 UTC  
> Updated at: 2017-08-28 05:35:19 UTC  
> Url: https://github.com/boostorg/thread/issues/133#issuecomment-325262185  

This is on Windows 10 insiders with Visual Studio 17.4 Preview.  I think it was also failing on 17.3 on windows 10, but I can't guarantee that.  I could rerun with regular 17.3.  This is a 4/8 sandy bridge machine.  (More detailed specs in other bugs).  This persisted seemingly independent of system load.

---

## Comment 3

> Username: viboes  
> Created at: 2017-08-28 06:34:38 UTC  
> Updated at: 2017-08-28 06:35:19 UTC  
> Url: https://github.com/boostorg/thread/issues/133#issuecomment-325271563  

Could you tell me which % threshold is good for Windows?  
  
I don't know, maybe it isn't worth running this test.

---

## Comment 4

> Username: garyfurnish  
> Created at: 2017-08-28 06:44:46 UTC  
> Url: https://github.com/boostorg/thread/issues/133#issuecomment-325273078  

If I run it alone the highest I've gotten was 13.8% and 15% might be a reasonable target.  
If I use b2 -j8 (num hyperthreads) to run the tests then it can go at least 38.2%, maybe higher.  I suspect this test will always fail if run in parallel with other tests.  Run alone its probably reasonable.

---

## Comment 5

> Username: viboes  
> Created at: 2017-08-28 07:00:37 UTC  
> Url: https://github.com/boostorg/thread/issues/133#issuecomment-325275770  

I guess that the testers are using -jx  
  
I will set the threshold to 40% :(

---

## Comment 6

> Username: viboes  
> Created at: 2017-08-28 07:17:44 UTC  
> Url: https://github.com/boostorg/thread/issues/133#issuecomment-325278810  

https://github.com/boostorg/thread/commit/3ee73c866de658a8e697ce74e62b05824fa93b5e

---

## Comment 7

> Username: garyfurnish  
> Created at: 2017-08-28 14:35:30 UTC  
> Url: https://github.com/boostorg/thread/issues/133#issuecomment-325371310  

Tests clean under load, closing as fixed.
