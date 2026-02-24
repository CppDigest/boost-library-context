# #167 Fix toolset version for VS2017 (should be 14.1) [Closed]

> Username: KindDragon  
> Created at: 2017-02-20 18:24:14 UTC  
> Updated at: 2021-10-02 22:18:52 UTC  
> Closed at: 2017-03-10 13:03:28 UTC  
> Url: https://github.com/boostorg/build/pull/167  

Related to #157

---

## Comment 1

> Username: teeks99  
> Created_at: 2017-02-26 12:53:03 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282553796  

Just to be clear, the version of the c++ compiler is "15", it is the CRT that is only being incremented to "14.1".  
  
https://build2.org/article/notes-visual-studio-15.xhtml

---

## Review 2 [Changes requested]

> Username: jhunold  
> Created_at: 2017-02-26 14:38:05 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/167#pullrequestreview-23883184  

There are wrong registry values being queried

📁 src/tools/msvc.jam

```diff
1626 | .version-14.0-reg = "VisualStudio\\14.0\\Setup\\VC" ;
1627 |- .version-15.0-reg = "VisualStudio\\15.0\\Setup\\VC" ;
1627 |+ .version-14.1-reg = "VisualStudio\\14.1\\Setup\\VC" ;
```

> Username: jhunold  
> Created_at: 2017-02-26 14:36:22 UTC  
> Updated_at: 2017-02-27 20:36:02 UTC  
> Url: https://github.com/boostorg/build/pull/167#discussion_r103106173  

This looks like classic copy&paste. I don't have any of those registry values. Any hints on the real registry values are welcome.

> Username: KindDragon  
> Created_at: 2017-02-27 03:20:03 UTC  
> Updated_at: 2017-02-27 20:36:02 UTC  
> Url: https://github.com/boostorg/build/pull/167#discussion_r103131538  

They don't have register keys for VS2017. Should I just remove it?  
How to support properly VS2017 path detection is  still being debated in #157

> Username: jhunold  
> Created_at: 2017-02-27 08:24:50 UTC  
> Updated_at: 2017-02-27 20:36:02 UTC  
> Url: https://github.com/boostorg/build/pull/167#discussion_r103153876  

Yes, it is better to remove them in this case. I've taken a look into #157 but I'm not sure which way to go. And I'm still not convinced that we call the thing "14.1"


📁 src/tools/msvc.py

```diff
1295 | __version_14_0_reg = "VisualStudio\\14.0\\Setup\\VC"
1296 |- __version_15_0_reg = "VisualStudio\\15.0\\Setup\\VC"
1296 |+ __version_14_1_reg = "VisualStudio\\14.1\\Setup\\VC"
```

> Username: jhunold  
> Created_at: 2017-02-26 14:37:12 UTC  
> Updated_at: 2017-02-27 20:36:02 UTC  
> Url: https://github.com/boostorg/build/pull/167#discussion_r103106181  

and copy&paster error, too.


---

## Comment 3

> Username: KindDragon  
> Created_at: 2017-02-27 20:53:55 UTC  
> Updated_at: 2017-02-27 21:02:06 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282849760  

> I've taken a look into #157 but I'm not sure which way to go. And I'm still not convinced that we call the thing "14.1"  
  
I think it's similar to VS 2013 - 7.1, so toolset should be 14.1. But for VS2013 IDE version was also 7.1 (IDE version for VS2017 15.0)

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2017-02-27 21:05:44 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282853050  

As far as I can remember, we've always used the version of the "VC", aka msvc, aka cl, as the true number. We've never used the CRT nor the IDE version.

---

## Comment 5

> Username: KindDragon  
> Created_at: 2017-02-27 21:44:25 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282864263  

```  
ECHO ### Toolsets supported by this script are: borland, como, gcc, gcc-nocygwin,  
ECHO ###     intel-win32, metrowerks, mingw, msvc, vc7, vc8, vc9, vc10, vc11, vc12, vc14  
```  
These versions match with ide version or toolset version (crt dll).  
Cl.exe version is very different https://build2.org/article/notes-visual-studio-15.xhtml

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2017-02-27 22:20:17 UTC  
> Updated_at: 2017-02-27 22:22:18 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282875358  

I stand corrected then :-) Are we sure the VS top-line version is 14.1 for VS 2017?  
  
PS.. Sorry if I'm late to the comments. Trying to catch up.

---

## Comment 7

> Username: teeks99  
> Created_at: 2017-02-27 22:25:58 UTC  
> Updated_at: 2017-02-27 22:26:56 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282876782  

The more I look at this, the more I wonder what microsoft was thinking...  
  
The directory that the visual studio tools are installed in is `%PROGRAMFILES%\MicrosoftVisualStudio\2017\[Community|Professional|Enterprise]\VC\Tools\MSVC\14.10.24930\bin\HostX[86|64]\x[86|64]\cl.exe`  
Note that is 14.10, not 14.1!  
  
Looking at the properties of cl.exe, the "File version" is 19.10.24930.0, the "Product version" is 14.10.24930.0.   
  
Looking at the runtime, in `%PROGRAMFILES%\MicrosoftVisualStudio\2017\[Community|Professional|Enterprise]\VC\Redist\MSVC\14.10.24930\x[86|64]\Microsoft.VC150.CRT\` you have `concrt140.dll`, `msvcp140.dll`, `vccorlib140.dll`, `vcruntime140.dll`.    
  
So there is a 14.10 directory, that contains modules (side-by-side assemblies?) labeled with "VC150", containing DLLs that specify "140". I don't think the runtime is generally relevant to us, but this is going to cause major confusion among developers!  
  
Looking at the versions in visual studio under help->about, the displayed info is:  
    Microsoft Visual Studio Community 2017 RC  
    VisualStudio/15.0.0-RC.4+26206.0  
  
The actual tool lines such as "Visual C++ 2017 RC" only have some kind of UID next to them that doesn't seem to translate into a version number (just like in VS2015).  
  
**I vote we call the toolset "msvc-15.0" and "vc15", as microsoft is loudly trumpeting this as visual studio version "15".**

---

## Comment 8

> Username: teeks99  
> Created_at: 2017-02-27 22:31:50 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282878374  

At one point I think there was a link to a microsoft blog post explaining all this posted to the mailing list, does anyone happen to have a link to that? I'm not having much luck finding it.

---

## Comment 9

> Username: teeks99  
> Created_at: 2017-02-27 22:42:14 UTC  
> Updated_at: 2017-02-27 22:42:44 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282880961  

I've also found [this page](https://www.visualstudio.com/en-us/productinfo/vs2017-install-product-Enterprise.workloads), updated today, that lists the various build tools as being 15.0.X.  
  
Specifically this line:  
Microsoft.VisualStudio.Component.VC.Tools.x86.x64 	VC++ 2017 v141 toolset (x86,x64) 	15.0.26109.1 	Recommended

---

## Comment 10

> Username: KindDragon  
> Created_at: 2017-02-27 22:46:10 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282881875  

> I've also found this page, updated today that lists the various build tools as being 15.0.X.  
  
I think this comment related to your question https://www.reddit.com/r/cpp/comments/5ut2bw/visual_studio_2017_isnt_supported_by_boost/ddzgtcm/  
  
> Microsoft.VisualStudio.Component.VC.Tools.x86.x64 VC++ 2017 v141 toolset (x86,x64) 15.0.26109.1  
  
It's toolset installer for VS2017, so it should be 15.00. But yes it's confusing

---

## Comment 11

> Username: KindDragon  
> Created_at: 2017-02-27 22:46:57 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282882047  

/cc @AndrewPardoe

---

## Comment 12

> Username: KindDragon  
> Created_at: 2017-02-27 22:48:40 UTC  
> Updated_at: 2017-02-27 22:49:36 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282882482  

> Are we sure the VS top-line version is 14.1 for VS 2017?  
  
Toolset version for VS2017 is 14.1. IDE version 15.00 https://github.com/boostorg/config/pull/119#issuecomment-281500274

---

## Comment 13

> Username: grafikrobot  
> Created_at: 2017-02-28 00:22:53 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282901545  

Well.. I see three choices given all the above links to docs:  
  
1. Follow the "toolset" version, aka version for Microsoft.VisualStudio.Component.VC.Tools.x86.x64.  
2. Follow the "top-line" version, aka what users will "see". Which would be version for Microsoft.VisualStudio.Component.VC.CoreIde (right?).  
3. Follow the "cl.exe" version.  
  
I don't see #1 as having any advantages for ourselves or for users. It's obscure enough to not be relevant. And it doesn't match what users might expect.  
  
#2 has the advantage that it's something users might point to visually when referring to what they have installed. But since it's my understanding that this version will not match the compiler itself, which is what we most care about, I don't see it as a big advantage.  
  
Last, #3.. It has the advantage that it's an easy version number to obtain for users. Also it's not susceptible to marketing version and packaged product versions fluctuations. It does have the disadvantage of producing a jump in the b2 toolset numbers. But I think that's a minor problem, if we don't go trying to back-fix numbers.  
  
So.. Maybe we should make it 19.10 (ie vc1910)?

---

## Comment 14

> Username: KindDragon  
> Created_at: 2017-02-28 01:18:25 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282911068  

But this is the version that would be in dll name, like `boost_filesystem-vc140-mt-1_6X.dll`.  
VS2017 can use dll's from VS2015 or VS2017, so I think they should be named `boost_filesystem-vc140-mt-1_6X.dll` and `boost_filesystem-vc141-mt-1_6X.dll`   
  
And we can't change this version for old VS versions

---

## Comment 15

> Username: KindDragon  
> Created_at: 2017-02-28 01:21:41 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282911620  

JFI Boost Library Naming http://www.boost.org/doc/libs/1_42_0/more/getting_started/unix-variants.html#library-naming

---

## Comment 16

> Username: KindDragon  
> Created_at: 2017-02-28 01:25:29 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-282912303  

IDK why Boost toolset enumeration contains version only for Visual Studio

---

## Comment 17

> Username: MaartenBent  
> Created_at: 2017-03-01 09:28:24 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-283289177  

Looking at the Platform Toolsets available for a project in Visual Studio 2017, It should indeed be 141:  
![vs-toolsets](https://cloud.githubusercontent.com/assets/8088070/23453721/b4303806-fe69-11e6-8a30-c50799e39cf9.png)

---

## Comment 18

> Username: refack  
> Created_at: 2017-03-06 14:49:04 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-284416604  

Do we have a bottom line for me to improve #170?

---

## Comment 19

> Username: KindDragon  
> Created_at: 2017-03-07 03:00:38 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-284607115  

Quick voting. Add reaction to this post 👍 if you think toolset should be 14.10, 👎 if you think it should remain 15.00, or 😕 if you uncertain

---

## Comment 20

> Username: refack  
> Created_at: 2017-03-07 13:25:04 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-284720094  

Voting with my feet at #170

---

## Comment 21

> Username: grafikrobot  
> Created_at: 2017-03-10 13:03:28 UTC  
> Url: https://github.com/boostorg/build/pull/167#issuecomment-285664019  

Changed it in develop to 1410.

---
