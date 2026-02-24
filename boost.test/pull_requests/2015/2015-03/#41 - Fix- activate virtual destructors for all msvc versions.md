# #41 Fix: activate virtual destructors for all msvc versions [Closed]

> Username: jhunold  
> Created at: 2015-03-08 16:45:47 UTC  
> Updated at: 2020-01-07 06:46:59 UTC  
> Closed at: 2019-12-15 19:19:51 UTC  
> Url: https://github.com/boostorg/test/pull/41  

All msvc version spew out warnings so make the destructors virtual for all.

---

## Comment 1

> Username: rogeeff  
> Created_at: 2015-03-08 21:51:45 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-77776737  

Why do we need this?  
  
On Sun, Mar 8, 2015 at 12:45 PM, Jürgen Hunold notifications@github.com  
wrote:  
  
> ## All msvc version spew out warnings so make the destructors virtual for all.  
>   
> You can view, comment on, or merge this pull request online at:  
>   
>   https://github.com/boostorg/test/pull/41  
> Commit Summary  
> - Fix: activate virtual destructors for all msvc versions  
>   
> File Changes  
> - _M_ include/boost/test/detail/config.hpp  
>   https://github.com/boostorg/test/pull/41/files#diff-0 (2)  
>   
> Patch Links:  
> - https://github.com/boostorg/test/pull/41.patch  
> - https://github.com/boostorg/test/pull/41.diff  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/41.  
  
##   
  
Gennadiy Rozental

---

## Comment 2

> Username: jhunold  
> Created_at: 2015-03-09 08:33:43 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-77816118  

msvc will issue warnings about the destructors being not virtual. There is no need to make this dependent on the compiler version as all version complain.   
  
Another solution would be to disable this via #pragma . Or just make the destructor virtual by default, as gcc and clang are already doing this.

---

## Comment 3

> Username: rogeeff  
> Created_at: 2015-03-11 08:16:45 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-78221101  

Can we file a bug report with MSVC compiler?  
  
On Mon, Mar 9, 2015 at 4:33 AM, Jürgen Hunold notifications@github.com  
wrote:  
  
> msvc will issue warnings about the destructors being not virtual. There is  
> no need to make this dependent on the compiler version as all version  
> complain.  
>   
> Another solution would be to disable this via #pragma . Or just make the  
> destructor virtual by default, as gcc and clang are already doing this.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/41#issuecomment-77816118.  
  
##   
  
Gennadiy Rozental

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2015-03-11 22:19:54 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-78385232  

FYI, I created the ticket https://svn.boost.org/trac/boost/ticket/11107. It looks to me that non-virtual is a bug, but I do not understand the first intention of this workaround. Any hint? (some more details in the ticket)

---

## Comment 5

> Username: rogeeff  
> Created_at: 2015-03-12 01:18:55 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-78407130  

destructor of base class should be either virtual or protected. I chose  
second option and its perfectly valid option - no need for any warnings  
  
On Wed, Mar 11, 2015 at 6:19 PM, Raffi Enficiaud notifications@github.com  
wrote:  
  
> FYI, I created the ticket https://svn.boost.org/trac/boost/ticket/11107.  
> It looks to me that non-virtual is a bug, but I do not understand the first  
> intention of this workaround. Any hint? (some more details in the ticket)  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/41#issuecomment-78385232.  
  
##   
  
Gennadiy Rozental

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2015-03-12 10:43:58 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-78457499  

You're right. However, protected + virtual does not harm neither? On the other hand, I cannot see any warning with `b2 --with-test`. @jhunold would you please attach the logs to the ticket please? We would then better understand what we are discussing.

---

## Comment 7

> Username: jhunold  
> Created_at: 2015-03-12 19:11:13 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-78574581  

Right, added full log to the ticket. I've just made the warning an error to just clarify things. @rogeeff All major compilers warn about this (gcc,clang and msvc) so I doubt they are all wrong. I've only found   
http://www.gotw.ca/publications/mill18.htm stating:  
  
```  
Guideline #4: A base class destructor should be either public and virtual, or protected and nonvirtual.  
  
True, the standard library itself does not always follow these design criteria. In part, that's a reflection of how we as a community have learned over the years.  
```  
  
So that is a guideline from 14 years ago. I*d really like to know if this is still valid or the exact paragraph of the standard handling this.

---

## Comment 8

> Username: raffienficiaud  
> Created_at: 2015-03-17 13:00:39 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-82332384  

Hi,  
  
Would you please try the develop branch again for this warning? The log you sent were not on the protected part, see commit 653aae587f9aaca8dfadee31a116f32bed025e8a

---

## Comment 9

> Username: jhunold  
> Created_at: 2015-03-18 12:32:31 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-82949238  

Uploaded a new log to the ticket. I had the patch active when doing the test run. I really should create the working branch before committing.

---

## Comment 10

> Username: raffienficiaud  
> Created_at: 2019-12-15 19:15:54 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-565837955  

Superseded/addressed by 13ca3e07f071ee36a0c5be5c6a0b58be7ab4637c

---

## Comment 11

> Username: raffienficiaud  
> Created_at: 2020-01-07 06:46:58 UTC  
> Url: https://github.com/boostorg/test/pull/41#issuecomment-571459261  

Merged to master

---
