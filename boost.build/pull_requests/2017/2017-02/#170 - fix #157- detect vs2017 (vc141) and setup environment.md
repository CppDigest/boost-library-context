# #170 fix #157: detect vs2017 (vc141) and setup environment [Merged]

> Username: refack  
> Created at: 2017-02-28 01:25:53 UTC  
> Updated at: 2021-10-02 22:18:40 UTC  
> Merged at: 2017-03-12 14:16:37 UTC  
> Closed at: 2017-03-12 14:16:37 UTC  
> Url: https://github.com/boostorg/build/pull/170  

Based on JIT compilation of a C# file and COM

---

## Review 1 [Commented]

> Username: KindDragon  
> Created_at: 2017-03-04 02:20:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/170#pullrequestreview-25104982  

📁 src/tools/msvc.jam

```diff
 927 |             # 9.0express as 9.0 here.
 910 |-             if [ MATCH "(Microsoft Visual Studio 15)" : $(command) ]
 928 |+             if [ MATCH "(14.10)" : $(command) ]
```

> Username: KindDragon  
> Created_at: 2017-03-04 02:20:09 UTC  
> Updated_at: 2017-03-10 23:48:29 UTC  
> Url: https://github.com/boostorg/build/pull/170#discussion_r104275699  

This line should be `if [ MATCH "(Microsoft Visual Studio[\/\\]2017)" : $(command) ]`, because they checking here path to Visual Studio

> Username: refack  
> Created_at: 2017-03-04 17:19:18 UTC  
> Updated_at: 2017-03-10 23:48:29 UTC  
> Url: https://github.com/boostorg/build/pull/170#discussion_r104292513  

I know but with 2017 that string is not a part of the path. Only predictable string is `14.10`

> Username: refack  
> Created_at: 2017-03-04 17:21:34 UTC  
> Updated_at: 2017-03-10 23:48:29 UTC  
> Url: https://github.com/boostorg/build/pull/170#discussion_r104292556  

On my machine it's `C:\bin\dev\VS\2017\BuildTools\VC\Tools\MSVC\14.10.24930\bin\HostX64\x64`

> Username: KindDragon  
> Created_at: 2017-03-05 01:03:22 UTC  
> Updated_at: 2017-03-10 23:48:29 UTC  
> Url: https://github.com/boostorg/build/pull/170#discussion_r104301330  

I think this codepath only for default path to VS, check expression for VS2015 below for example

> Username: refack  
> Created_at: 2017-03-05 17:34:55 UTC  
> Updated_at: 2017-03-10 23:48:29 UTC  
> Url: https://github.com/boostorg/build/pull/170#discussion_r104318520  

It's logic to deduce the VS version from a given path. `14.10` is the only stable path part (`MSVC\14.10` at most).  
There are other patterns in this block RE: [line 965](https://github.com/refack/build/blob/4973c19e47efbe338664145818fdc00fb24759e9/src/tools/msvc.jam#L965)  
VS2017 setup has been reworked extensively...


---

## Comment 2

> Username: KindDragon  
> Created_at: 2017-03-04 02:24:17 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-284119736  

Maybe transfer functionality for debugging in a separate PR to speed up the merger of this PR?

---

## Comment 3

> Username: refack  
> Created_at: 2017-03-04 17:28:11 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-284166711  

@KindDragon what debugging functionality specifically?

---

## Comment 4

> Username: KindDragon  
> Created_at: 2017-03-05 01:10:16 UTC  
> Updated_at: 2017-03-05 01:11:36 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-284197136  

Ahh, your PR based on changes from 'debug'  branch.  I think you should remove this commits from your PR

---

## Comment 5

> Username: refack  
> Created_at: 2017-03-05 17:43:06 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-284246252  

Branch base oops (`debug` and `develop` look too much alike for a dysxelic ;)

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2017-03-05 17:48:38 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-284246613  

General question.. From the other pulls/issues/emails.. Shouldn't the version number be "14.10" or "1410", i.e. not 15.0, nor 14.1, nor 141?

---

## Comment 7

> Username: refack  
> Created_at: 2017-03-05 17:49:33 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-284246680  

👍 14.10

---

## Comment 8

> Username: refack  
> Created_at: 2017-03-05 17:51:32 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-284246823  

@grafikrobot actually it has nuance, The "whole" Visual Studio suite will be version 15.0, but the C++ toolset will be 14.10

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2017-03-05 17:55:34 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-284247089  

@refack I wouldn't call that a nuance.. More like a total clusterf_ck on the part of Microsoft.

---

## Comment 10

> Username: refack  
> Created_at: 2017-03-05 17:58:17 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-284247290  

For this project `15.0` is used only as an internal moniker, so can consider it a nuance.... 🤷  
RE: dotnet/core#509

---

## Comment 11

> Username: KindDragon  
> Created_at: 2017-03-06 03:00:28 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-284291427  

@refack Discussion about 14.10 in PR https://github.com/boostorg/build/pull/167

---

## Review 12 [Commented]

> Username: grafikrobot  
> Created_at: 2017-03-08 00:22:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/170#pullrequestreview-25669341  

📁 src/tools/vc141helper/GetVS2017Configuration.cs

```diff
   1 |+ ﻿// powershell -ExecutionPolicy Unrestricted -Version "2.0" -Command "&{ Add-Type -Path Program.cs; [VisualStudioConfiguration.Program]::Main(@())}"
```

> Username: grafikrobot  
> Created_at: 2017-03-08 00:22:31 UTC  
> Updated_at: 2017-03-10 23:48:29 UTC  
> Url: https://github.com/boostorg/build/pull/170#discussion_r104815938  

This needs a license / copyright statement to be considered for inclusion in Boost.

> Username: refack  
> Created_at: 2017-03-08 00:41:32 UTC  
> Updated_at: 2017-03-10 23:48:29 UTC  
> Url: https://github.com/boostorg/build/pull/170#discussion_r104818608  

done


---

## Comment 13

> Username: grafikrobot  
> Created_at: 2017-03-10 12:44:09 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-285660117  

After installing VS2017 community I did:  
  
```  
C:\>cd DevRoots\Boost\build-refack\src\tools\vc141helper  
  
C:\DevRoots\Boost\build-refack\src\tools\vc141helper>cl141_path.cmd  
"COM Ok"  
  
C:\DevRoots\Boost\build-refack\src\tools\vc141helper>  
```  
  
And as you can see it did not detect my install. Which is installed at the default location.

---

## Comment 14

> Username: refack  
> Created_at: 2017-03-10 13:34:26 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-285669944  

@grafikrobot checking

---

## Comment 15

> Username: refack  
> Created_at: 2017-03-10 13:47:59 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-285672740  

@grafikrobot can you gist the output of `try_powershell.cmd` just to make sure you have the VCTools "workload" installed.  
[Here's mine](https://gist.github.com/refack/a0868d2d2c3865dd3b2c8aaca0d361b4#file-output-josn-L80)

---

## Comment 16

> Username: grafikrobot  
> Created_at: 2017-03-10 14:03:26 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-285676378  

@refack Sure.. I posted it as a comment on your gist.

---

## Comment 17

> Username: refack  
> Created_at: 2017-03-10 19:13:07 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-285758188  

@grafikrobot I tried to simplify the non C# part, and added some fallbacks.

---

## Comment 18

> Username: refack  
> Created_at: 2017-03-11 00:21:24 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-285820899  

[CI machine](https://ci.appveyor.com/project/refack/windows-autoconf/build/job/tksj2vuno0aajr1o) passes

---

## Comment 19

> Username: jhunold  
> Created_at: 2017-03-11 08:53:48 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-285853744  

Tested the last version of the patch with 2017 Enterprise. Works

---

## Comment 20

> Username: refack  
> Created_at: 2017-03-12 14:18:45 UTC  
> Updated_at: 2017-03-12 14:19:39 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-285947634  

One small step for me, kinda small step for `Boost.build` 🐱‍🚀  
@grafikrobot much respect! 🥇

---

## Comment 21

> Username: AndrewPardoe  
> Created_at: 2017-04-20 22:41:26 UTC  
> Url: https://github.com/boostorg/build/pull/170#issuecomment-295949051  

The officially supported vswhere tool is now available as part of the install starting today with Visual Studio 15.2 preview 2: https://blogs.msdn.microsoft.com/heaths/2017/04/21/vswhere-is-now-installed-with-visual-studio-2017/. This doesn't fix all the existing installs of VS 2017 but going forward you can now detect the VS install locations and whether C++ tools are available from a scripted environment. I hope this helps your scenario.

---
