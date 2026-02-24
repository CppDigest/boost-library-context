# #281 Add zstd.jam file. [Merged]

> Username: rdoeffinger  
> Created at: 2018-01-14 13:15:08 UTC  
> Updated at: 2021-10-02 22:08:20 UTC  
> Merged at: 2018-01-16 19:23:25 UTC  
> Closed at: 2018-01-16 19:23:25 UTC  
> Url: https://github.com/boostorg/build/pull/281  

Just a copy-paste of lzma.jam.  
Will be used for zstd compression/decompression support  
in iostreams (see also trac ticket #13356).  
  
Rebased against develop from https://github.com/boostorg/build/pull/280  
Patch needing this is https://github.com/boostorg/iostreams/pull/48

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2018-01-14 16:58:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/281#pullrequestreview-88688472  

📁 src/tools/zstd.jam

```diff
  10 |+ # After 'using zstd', the following targets are available:
  11 |+ #
  12 |+ # /zstd//lzam -- The zstd library
```

> Username: pdimov  
> Created_at: 2018-01-14 16:58:51 UTC  
> Updated_at: 2018-01-15 19:10:39 UTC  
> Url: https://github.com/boostorg/build/pull/281#discussion_r161403620  

Should probably be `/zstd//zstd`; also hints at a typo in `lzma.jam` (`lzam`).


---

## Comment 2

> Username: rdoeffinger  
> Created_at: 2018-01-15 18:34:06 UTC  
> Url: https://github.com/boostorg/build/pull/281#issuecomment-357759071  

I fixed the typo-caused lzam left-over.  
I also added a zstd.py (though I probably should have named it libzstd.py or what do you think?) to test/  
It seems to work/pass when I run it manually, however I am not sure since I am not able to run the whole testsuite, I get this very weird error when I try to run bjam in that directory:  
  
Traceback (most recent call last):  
  File "/usr/lib/python2.7/site.py", line 68, in <module>  
    import os  
  File "/usr/lib/python2.7/os.py", line 49, in <module>  
    import posixpath as path  
  File "/usr/lib/python2.7/posixpath.py", line 17, in <module>  
    import warnings  
  File "/usr/lib/python2.7/warnings.py", line 8, in <module>  
    import types  
  File "types.py", line 13, in <module>  
    import BoostBuild  
  File "BoostBuild.py", line 8, in <module>  
    import TestCmd  
  File "TestCmd.py", line 51, in <module>  
    from string import join, split  
  File "/usr/lib/python2.7/string.py", line 83, in <module>  
    import re as _re  
  File "/usr/lib/python2.7/re.py", line 296, in <module>  
    import copy_reg  
  File "/usr/lib/python2.7/copy_reg.py", line 7, in <module>  
    from types import ClassType as _ClassType  
ImportError: cannot import name ClassType

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-01-15 18:48:20 UTC  
> Url: https://github.com/boostorg/build/pull/281#issuecomment-357761814  

```  
File "/usr/lib/python2.7/warnings.py", line 8, in   
 import types  
 File "types.py", line 13, in   
 import BoostBuild  
```  
  
The built-in `warnings.py` tries to import the built-in `types.py` but ends up with `tools/build/test/types.py` for some reason.  
  
I remember having a similar problem, but no longer recall how or whether I got around it.

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-01-15 18:50:32 UTC  
> Url: https://github.com/boostorg/build/pull/281#issuecomment-357762240  

A-ha: https://github.com/boostorg/build/commit/94d2cfcdb7c9ff620d1afc065b1466640150bb86  
  
I had the problem with `zlib.py`, which the above commit renames.

---

## Comment 5

> Username: rdoeffinger  
> Created_at: 2018-01-15 19:03:34 UTC  
> Url: https://github.com/boostorg/build/pull/281#issuecomment-357764931  

That commit doesn't help for me, wouldn't the more obvious solution be to rename the types.py test to something else?  
And what is the meaning of the test_all.py file? Should it list all test scripts? Because e.g. libtiff seems to be missing! And why is it not executable?

---

## Comment 6

> Username: swatanabe  
> Created_at: 2018-01-15 21:44:33 UTC  
> Url: https://github.com/boostorg/build/pull/281#issuecomment-357795917  

AMDG  
  
On 01/15/2018 11:34 AM, Reimar Döffinger wrote:  
> I also added a zstd.py (though I probably should have named it libzstd.py or what do you think?)  
  
zstd.py is fine.  I haven't quite decided on what convention to use  
for naming.  I originally intended to have the test name the  
same as the module name, but that ran into problems with zlib  
in python 2.7.14.  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: swatanabe  
> Created_at: 2018-01-15 22:07:15 UTC  
> Url: https://github.com/boostorg/build/pull/281#issuecomment-357799807  

AMDG  
  
On 01/15/2018 12:03 PM, Reimar Döffinger wrote:  
> That commit doesn't help for me, wouldn't the more obvious solution be to rename the types.py test to something else?  
> And what is the meaning of the test_all.py file? Should it list all test scripts? Because e.g. libtiff seems to be missing! And why is it not executable?  
>   
  
  libtiff.py was accidentally missed when it was  
originally added.  It's probably broken right now  
and I haven't gotten around to checking it.  As for  
why test_all.py isn't executable, I don't think there's  
any reason other than that whoever added it forget to set  
the executable bit.  
  
In Christ,  
Steven Watanabe

---
