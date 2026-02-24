# #275 Add xcode 8, 7.3, 6.4 to Travis [Merged]

> Username: pdimov  
> Created at: 2017-12-22 17:41:48 UTC  
> Updated at: 2021-10-02 22:08:25 UTC  
> Merged at: 2017-12-23 17:13:41 UTC  
> Closed at: 2017-12-23 17:13:41 UTC  
> Url: https://github.com/boostorg/build/pull/275  



---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2017-12-22 17:49:29 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/275#pullrequestreview-85365291  

I would rather avoid having multiple minor versions. I.e. we already have 8.3, hence I don't see the point in having 8gm in here. And since we are adding.. We would also need to add 9.2.

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-12-22 17:56:03 UTC  
> Url: https://github.com/boostorg/build/pull/275#issuecomment-353646732  

Well the point of having 8 is that currently it fails, whereas 8.3 succeeds - see https://travis-ci.org/boostorg/config/builds/318188812.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2017-12-22 17:56:28 UTC  
> Url: https://github.com/boostorg/build/pull/275#issuecomment-353646806  

AMDG  
  
On 12/22/2017 10:49 AM, Rene Rivera wrote:  
> I would rather avoid having multiple minor versions. I.e. we already have 8.3, hence I don't see the point in having 8gm in here.  
>   
  
It doesn't seem pointless, given that 8.3 currently passes,  
but 8.0 appears to fail.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-12-22 17:59:59 UTC  
> Url: https://github.com/boostorg/build/pull/275#issuecomment-353647409  

I agree that it would be prudent to have everything from https://docs.travis-ci.com/user/reference/osx/#OS-X-Version here, that is, add 9/9.1/9.2 as well, but OS X jobs are very slow, so one needs to carefully pick and choose which ones to have. :-)  
  
Once we see the results so that the failure can be take care of, I'll update the commit with whatever versions we decide to add or keep.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-12-22 18:16:40 UTC  
> Url: https://github.com/boostorg/build/pull/275#issuecomment-353650112  

OK, expanded to the full one after deliberation. :-)

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-12-23 00:20:59 UTC  
> Url: https://github.com/boostorg/build/pull/275#issuecomment-353694340  

Building on `precise` fails too:  
  
```  
gcc -o bootstrap/jam0 command.c compile.c constants.c debug.c execcmd.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c class.c cwd.c native.c md5.c w32_getreg.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c execunix.c fileunix.c pathunix.c  
/tmp/ccENHHLg.o: In function `timestamp_current':  
timestamp.c:(.text+0xb0): undefined reference to `clock_gettime'  
collect2: ld returned 1 exit status  
```  
  
https://travis-ci.org/boostorg/build/jobs/320333540#L158

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-12-23 00:49:57 UTC  
> Url: https://github.com/boostorg/build/pull/275#issuecomment-353696582  

So OS X defines `_POSIX_TIMERS` but not `clock_gettime` or `CLOCK_REALTIME`, just to make things more interesting. :-)  
  
Incidentally, on Travis `./build.sh` always uses the default toolset and not `TOOLSET`. This could be intentional, although I doubt it.

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-12-23 00:59:30 UTC  
> Url: https://github.com/boostorg/build/pull/275#issuecomment-353697092  

Re precise, apparently, `clock_gettime` is in glibc since 2.17, before that, one needs `-lrt`.

---

## Comment 9

> Username: swatanabe  
> Created_at: 2017-12-23 01:01:15 UTC  
> Url: https://github.com/boostorg/build/pull/275#issuecomment-353697206  

AMDG  
  
On 12/22/2017 05:20 PM, Peter Dimov wrote:  
> timestamp.c:(.text+0xb0): undefined reference to `clock_gettime'  
  
I guess we need -lrt?  I'll probably just disable this for  
bootstrapping, and put any more complex checks in build.jam.  
  
In Christ,  
Steven Watanabe

---
