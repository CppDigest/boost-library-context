# #378 Add support for SaxonHE for xsl processing [Merged]

> Username: djarek  
> Created at: 2018-12-19 23:24:55 UTC  
> Updated at: 2021-10-02 21:19:22 UTC  
> Merged at: 2019-02-15 19:37:06 UTC  
> Closed at: 2019-02-15 19:37:06 UTC  
> Url: https://github.com/boostorg/build/pull/378  

Will be used in generation of Beast reference.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-12-19 23:56:07 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-448795387  

AMDG  
  
- C:\Boost\bin seems like an odd place to search for java.  
- Use common.get-invocation-command instead of rolling your own.  
- You should probably search common installation locations  
  for the .jar, e.g. /usr/share/java/saxon/Saxon-HE.jar.  
- There's no need for extra indirection in the saxonhe rule:  
  actions saxonhe  
  {  
    "$(.java_exe)" -jar $(.saxonhe_jar) -o:$(<) -s:$(>[1]) -xsl:$(>[2])  
  }  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: djarek  
> Created_at: 2018-12-20 01:39:56 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-448827085  

@swatanabe   
Most of this was copy-pasted from xsltproc.jam's implementation. Should I add a default path to search the jar for on Windows?

---

## Comment 3

> Username: swatanabe  
> Created_at: 2018-12-20 02:38:14 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-448837798  

AMDG  
  
On 12/19/2018 06:39 PM, Damian Jarek wrote:  
> @swatanabe   
> Most of this was copy-pasted from xsltproc.jam's implementation.  
  
The extra complexity in xsltproc is because it's doing more.  
  
> Should I add a default path to search the jar for on Windows?  
>   
  
Is there a standard install location on Windows?  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: djarek  
> Created_at: 2018-12-20 21:07:16 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-449135563  

Hmm, couldn't find anything about a standard location for *.jar files on Windows, so I presume there is none.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-12-20 21:14:48 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-449137541  

AMDG  
  
On 12/20/2018 02:07 PM, Damian Jarek wrote:  
> Hmm, couldn't find anything about a standard location for *.jar files on Windows, so I presume there is none.  
>   
  
That's what it looks like to me, too.  There's  
no installer.  You just unzip the archive  
somewhere.  You might make it so that it works  
as long as the .jar is in CLASSPATH, though.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2018-12-23 03:33:23 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-449612016  

What else is needed from this pull request in order to merge?

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2019-01-19 18:04:33 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-455801934  

So...what's the hold up here?

---

## Comment 8

> Username: swatanabe  
> Created_at: 2019-01-19 20:59:49 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-455814720  

AMDG  
  
On 1/19/19 11:04 AM, Vinnie Falco wrote:  
> So...what's the hold up here?  
>   
  
Christmas got in the way, and then I forgot about it.  
  
Anyway,  
- get-invocation-command should only be used for  
  executables, not for .jars, since it searches PATH.  
- The default install location on Fedora is /usr/share/java/saxon/,  
  so it should probably be listed in addition to /usr/share/java.  
- The action needs to make sure that all file paths are quoted.  
  
In Christ,  
Steven Watanabe

---

## Comment 9

> Username: djarek  
> Created_at: 2019-02-02 02:11:33 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-459925818  

What should I use instead of `get-invocation-command`?

---

## Comment 10

> Username: swatanabe  
> Created_at: 2019-02-02 04:07:25 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-459933363  

AMDG  
  
On 2/1/19 7:11 PM, Damian Jarek wrote:  
> What should I use instead of `get-invocation-command`?  
>   
  
Just use path.glob like I think you used  
in the original PR.  Executables need special,  
platform specific, handling, which is why there's  
a special rule for them.  .jars are simpler,  
so a basic glob is good enough.  
  
In Christ,  
Steven Watanabe

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2019-02-11 04:34:15 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-462217520  

Just out of curiosity, what would that look like exactly if it was formed into a complete, working pull request?

---

## Comment 12

> Username: djarek  
> Created_at: 2019-02-11 14:56:02 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-462356831  

Changed the init rule so now it uses a regular glob to find the saxon jars.

---

## Review 13 [Commented]

> Username: grafikrobot  
> Created_at: 2019-02-11 15:16:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/378#pullrequestreview-202159372  

📁 src/tools/saxonhe.jam

```diff
  16 |+     else
  17 |+     {
  18 |+         local jar = [ GLOB $(path) "/usr/share/java/saxon/" "/usr/share/java/" : Saxon-HE.jar ] ;
```

> Username: grafikrobot  
> Created_at: 2019-02-11 15:16:10 UTC  
> Updated_at: 2019-02-13 22:00:42 UTC  
> Url: https://github.com/boostorg/build/pull/378#discussion_r255549991  

This doesn't look right. The `path` variable is used here but not defined elsewhere. Which means that it will always be empty.

> Username: djarek  
> Created_at: 2019-02-11 17:33:26 UTC  
> Updated_at: 2019-02-13 22:00:42 UTC  
> Url: https://github.com/boostorg/build/pull/378#discussion_r255614123  

Hmm, no idea where I got that from, anyway, removed the use of `$path`.


---

## Comment 14

> Username: swatanabe  
> Created_at: 2019-02-11 17:46:28 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-462424078  

AMDG  
  
The init rule looks good to me now.  You just  
need to quote all paths in the action.  
  
In Christ,  
Steven Watanabe

---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2019-02-11 18:43:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/378#pullrequestreview-202273466  

📁 src/tools/saxonhe.jam

```diff
   4 |+ # Distributed under the Boost Software License, Version 1.0. (See accompanying
   5 |+ # file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
   6 |+ 
```

> Username: vinniefalco  
> Created_at: 2019-02-11 18:43:44 UTC  
> Updated_at: 2019-02-13 22:00:42 UTC  
> Url: https://github.com/boostorg/build/pull/378#discussion_r255641291  

I would throw another pound sign on its own line here but that's just me


---

## Comment 16

> Username: djarek  
> Created_at: 2019-02-13 22:01:20 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-463392849  

All paths in the action are now quoted.

---

## Comment 17

> Username: swatanabe  
> Created_at: 2019-02-15 19:16:38 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-464166603  

AMDG  
  
On 2/13/19 3:01 PM, Damian Jarek wrote:  
> All paths in the action are now quoted.  
>   
  
Looks good to me.  
  
In Christ,  
Steven Watanabe

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2019-02-15 19:24:35 UTC  
> Url: https://github.com/boostorg/build/pull/378#issuecomment-464169185  

**HALLELUJAH** !!!!

---
