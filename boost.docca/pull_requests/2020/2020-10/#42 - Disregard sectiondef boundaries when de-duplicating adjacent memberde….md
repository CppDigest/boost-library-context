# #42 Disregard sectiondef boundaries when de-duplicating adjacent memberde… [Merged]

> Username: evanlenz  
> Created at: 2020-10-09 06:58:11 UTC  
> Updated at: 2020-10-19 06:11:07 UTC  
> Merged at: 2020-10-17 03:56:34 UTC  
> Closed at: 2020-10-17 03:56:34 UTC  
> Url: https://github.com/boostorg/docca/pull/42  

…f briefdescriptions (like "Constructor"). Addresses boostorg/json#440

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-10-09 15:58:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/docca/pull/42#pullrequestreview-505803525  

📁 include/docca/base-stage1.xsl

```diff
 173 |           <xsl:template mode="overload-list" match="memberdef">
 174 |-             <xsl:apply-templates select="briefdescription[not(. = ../preceding-sibling::*/briefdescription)]"/>
 174 |+             <xsl:apply-templates select="briefdescription[not(. eq preceding::briefdescription[parent::memberdef][1])]"/>
```

> Username: vinniefalco  
> Created_at: 2020-10-09 15:58:46 UTC  
> Updated_at: 2020-10-09 19:06:00 UTC  
> Url: https://github.com/boostorg/docca/pull/42#discussion_r502527591  

LOL That's it?!

> Username: evanlenz  
> Created_at: 2020-10-09 19:03:45 UTC  
> Updated_at: 2020-10-09 19:06:00 UTC  
> Url: https://github.com/boostorg/docca/pull/42#discussion_r502620901  

Yeah, although the `preceding` axis can be slow. I'm going to check in a better implementation shortly.


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-10-09 18:29:58 UTC  
> Url: https://github.com/boostorg/docca/pull/42#issuecomment-706338240  

Does the example build for you, with "b2 example" ?

---

## Comment 3

> Username: evanlenz  
> Created_at: 2020-10-09 18:41:10 UTC  
> Url: https://github.com/boostorg/docca/pull/42#issuecomment-706343067  

I'm getting this:  
  
evanl@THINKPAD-T480 ~/boost/tools/docca/example  
$ ../../../b2.exe  
Jamfile:33: in modules.load  
*** argument error  
* rule glob ( wildcards + : excludes * )  
* called with: (  )  
* missing argument wildcards  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:1274:see  
definition of rule 'glob' being called  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:372: in  
load-jamfile  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:64: in load  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:142: in  
project.find  
C:/cygwin64/home/evanl/boost/tools/build/src\build-system.jam:618: in load  
C:/cygwin64/home/evanl/boost/tools/build/src/kernel\modules.jam:295: in  
import  
C:/cygwin64/home/evanl/boost/tools/build/src/kernel/bootstrap.jam:139: in  
boost-build  
C:/cygwin64/home/evanl/boost/boost-build.jam:17: in module scope  
  
On Fri, Oct 9, 2020 at 11:30 AM Vinnie Falco <notifications@github.com>  
wrote:  
  
> Does the example build for you, with "b2 example" ?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/docca/pull/42#issuecomment-706338240>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAETWLWFE4RQ4QXC26YZWPTSJ5JDPANCNFSM4SJWC4QQ>  
> .  
>

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-10-09 18:45:59 UTC  
> Url: https://github.com/boostorg/docca/pull/42#issuecomment-706345216  

I am investigating...

---

## Comment 5

> Username: evanlenz  
> Created_at: 2020-10-13 02:42:16 UTC  
> Url: https://github.com/boostorg/docca/pull/42#issuecomment-707448952  

Happily, this code change should also fix boostorg/json#387, because now it is displaying the briefdescription if it has a different value than the immediately preceding briefdescription (not a different value than *any* of the previous briefdescriptions).

---
