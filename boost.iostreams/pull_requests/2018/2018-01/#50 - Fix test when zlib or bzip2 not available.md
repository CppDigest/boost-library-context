# #50 Fix test when zlib or bzip2 not available. [Merged]

> Username: rdoeffinger  
> Created at: 2018-01-19 21:35:28 UTC  
> Updated at: 2018-01-20 00:20:58 UTC  
> Merged at: 2018-01-20 00:20:58 UTC  
> Closed at: 2018-01-20 00:20:58 UTC  
> Url: https://github.com/boostorg/iostreams/pull/50  

Do not try to run tests when the dependencies  
are not available.  
Should fix the AppVeyor build.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-01-19 22:18:22 UTC  
> Url: https://github.com/boostorg/iostreams/pull/50#issuecomment-359105187  

I do not know how ac.check-library is supposed to work but there have been some problems with this in the past so I am loath to merge this PR. See [https://svn.boost.org/trac10/ticket/9156](url)

---

## Comment 2

> Username: swatanabe  
> Created_at: 2018-01-19 22:43:38 UTC  
> Url: https://github.com/boostorg/iostreams/pull/50#issuecomment-359110425  

AMDG  
  
On 01/19/2018 03:18 PM, Edward Diener wrote:  
> I do not know how ac.check-library is supposed to work but there have been some problems with this in the past so I am loath to merge this PR. See [https://svn.boost.org/trac10/ticket/9156](url)  
>   
  
  It's safe in this case, because it's inside the check for  
NO_ZLIB etc.  The problem in that ticket was a result  
of using /zlib//zlib unconditionally (I suppose I  
should close the ticket, since it's been fixed for  
a while.).  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-01-19 22:53:46 UTC  
> Updated_at: 2018-01-19 22:54:17 UTC  
> Url: https://github.com/boostorg/iostreams/pull/50#issuecomment-359112406  

What does a notation like 'ac.check-library /bzip2//bzip2' actually do ( I understand the \<build\>no part ) ? Boost has no library called 'bzip2' and I thought that the /xxx//xxx notation has to refer to another Boost library. Does it actually check for a 'using bzip2 etc.' somewhere in a jamfile ( mine is in user-config.jam ) ? I am just trying to understand how this actually works to ensure that the test gets run when the end-user specifies a toolset for bzip2.

---

## Comment 4

> Username: swatanabe  
> Created_at: 2018-01-19 23:14:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/50#issuecomment-359116191  

AMDG  
  
On 01/19/2018 03:53 PM, Edward Diener wrote:  
> What does a notation like 'ac.check-library /bzip2//bzip2' actually do ( I understand the <build>no part ) ?  
  
  If the named main target does not exist, it gives an error.  
(i.e. ac.check-library /bzip2//bzip2 will cause a hard  
error unless there is a `using bzip` somewhere.)  
If the target exists, and is usable, then the true-properties  
will be applied.  If the target has <build>no applied or is  
an ac-library which cannot be found or generates nothing  
for any other reason, then the test is considered to fail  
and the false-properties will be applied.  In this case,  
/bzip2//bzip2 is an ac-library, and the false-properties  
are <build>no, so the end result is that <build>no will  
be added iff autodetection of bzip2 failed.  (Note that  
if you explicitly specify bzip2, Boost.Build will still  
check that the parameters that you gave are usable.)  
  
> Boost has no library called 'bzip2' and I thought that the /xxx//xxx notation has to refer to another Boost library.  
  
  This is the general notation for referring to any  
target, not just a Boost library.  /boost//xxx refers  
to a library in the /boost project.  /zlib//zlib refers  
to the target named zlib in the /zlib project (which is  
defined when you say `using zlib`).  ../build//boost_iostreams  
names a target relative to the current Jamfile's location.  
  
> Does it actually check for a 'using bzip2 etc.' somewhere in a jamfile ( mine is in user-config.jam ) ?  
  
  The iostreams/build/Jamfile.v2 has a `using bzip2` (which  
will have no effect if you already have `using bzip2`  
in your user-config.jam.).  
  
> I am just trying to understand how this actually works to ensure that the test gets run when the end-user specifies a toolset for bzip2.  
>   
  
  The test needs to be run under exactly the same conditions  
that bzip2.cpp is linked into the boost_iostreams library.  
  
In Christ,  
Steven Watanabe

---
