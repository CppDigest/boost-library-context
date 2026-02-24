# #179 more for fix #157: update detect vs2017 scripts to v1.11.0 [Closed]

> Username: refack  
> Created at: 2017-03-17 20:06:26 UTC  
> Updated at: 2021-10-02 22:18:39 UTC  
> Closed at: 2017-04-20 21:49:16 UTC  
> Url: https://github.com/boostorg/build/pull/179  



---

## Comment 1

> Username: KindDragon  
> Created_at: 2017-03-19 11:14:01 UTC  
> Updated_at: 2017-03-19 11:14:12 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287609401  

Hi. I reworked your script in PR #173 to support checking `Microsoft.VisualCpp.Tools.Core` version and return only path string instead of JSON

---

## Review 2 [Commented]

> Username: KindDragon  
> Created_at: 2017-03-19 11:14:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/179#pullrequestreview-27745311  

📁 src/tools/vc141helper/GetKey.ps1

```diff
  11 |+ )
  12 |+ Add-Type -Path GetVS2017Configuration.cs;
  13 |+ $inst = ([VisualStudioConfiguration.ComSurrogate]::QueryEx($filter))[0]
```

> Username: KindDragon  
> Created_at: 2017-03-19 11:14:50 UTC  
> Updated_at: 2017-03-19 19:56:55 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106803867  

It can be empty list

> Username: refack  
> Created_at: 2017-03-19 11:56:34 UTC  
> Updated_at: 2017-03-19 19:56:55 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106804672  

Changed


---

## Comment 3

> Username: refack  
> Created_at: 2017-03-19 12:01:22 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287611763  

@KindDragon I also refactored the JSON code to a different file, added "IsVcCompatible" predicate, that checks for the minimal VC triad: `"SDK" && "MSBuild" && "VisualCppTools"`, and streamlined the `.ps1`

---

## Comment 4

> Username: refack  
> Created_at: 2017-03-19 12:02:06 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287611791  

@KindDragon P.S. how did you get github to acknowledge AppYevor?

---

## Comment 5

> Username: KindDragon  
> Created_at: 2017-03-19 12:25:52 UTC  
> Updated_at: 2017-03-19 12:26:10 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287613001  

> @KindDragon I also refactored the JSON code to a different file, added "IsVcCompatible" predicate, that checks for the minimal VC triad: "SDK" && "MSBuild" && "VisualCppTools", and streamlined the .ps1  
  
But you not checking visual studio version  
  
> @KindDragon P.S. how did you get github to acknowledge AppYevor?  
  
AppVeyor build every PR for this repository https://ci.appveyor.com/project/boostorg/build/build/1.0.106  
  
BTW because of PowerShell tests run 33 minutes for VS2017 and only 6 min for other versions. I will try switching to `vswhere` to speed up tests

---

## Comment 6

> Username: refack  
> Created_at: 2017-03-19 12:30:16 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287613245  

> But you not checking visual studio version  
  
What do you mean? You meen future proofing (since for now it's only 15.0)?  
  
> BTW because of PowerShell tests run 33 minutes for VS2017 and only 6 min for other versions. I will try switching to vswhere to speed up tests  
  
Let me look into that... (since the actual `bootstrap.bat` and `bjam` work reasonably fast...)

---

## Comment 7

> Username: KindDragon  
> Created_at: 2017-03-19 12:41:18 UTC  
> Updated_at: 2017-03-19 12:53:52 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287613789  

> What do you mean? You meen future proofing (since for now it's only 15.0)?  
  
EDIT: This probably will not probably work for next VS after 2017  
  
> Let me look into that... (since the actual bootstrap.bat and bjam work reasonably fast...)  
  
I see in Process Explorer spawning a lot of PowerShell executions. I think each test run your script many times

---

## Comment 8

> Username: refack  
> Created_at: 2017-03-19 12:44:30 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287613994  

> I see in Process Explorer spawning a lot of PowerShell executions. I think each test run your script many times  
  
That's a bug i've seen in `bootstrap.bat` when something fails...

---

## Comment 9

> Username: refack  
> Created_at: 2017-03-19 12:49:41 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287614320  

I.E. look at how it fails https://ci.appveyor.com/project/boostorg/build/build/1.0.106/job/1fabtlet1fdlmr6o  
`******  B A T C H   R E C U R S I O N  exceeds STACK limits ******`

---

## Comment 10

> Username: KindDragon  
> Created_at: 2017-03-19 12:52:48 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287614527  

> I.E. look at how it fails https://ci.appveyor.com/project/boostorg/build/build/1.0.106/job/1fabtlet1fdlmr6o  
> ****** B A T C H R E C U R S I O N exceeds STACK limits ******  
  
I fixed this in my PR by splitting build.bat to several files. I tried many options, but only it helped

---

## Comment 11

> Username: refack  
> Created_at: 2017-03-19 12:54:11 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287614616  

Yea I can relate. `cmd` is super fickle 🤦

---

## Comment 12

> Username: refack  
> Created_at: 2017-03-19 19:43:29 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287641770  

Green on [my fork](https://ci.appveyor.com/project/refack/build/build/1.0.8) in good time  
  
@grafikrobot you can cancel all others builds

---

## Comment 13

> Username: refack  
> Created_at: 2017-03-20 00:00:18 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-287658870  

@grafikrobot would you consider adding the COM helper tools as a [git submodule](https://github.com/node4good/msvs-com-helper)?

---

## Review 14 [Commented]

> Username: KindDragon  
> Created_at: 2017-03-20 02:22:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/179#pullrequestreview-27767861  

📁 test/test_all.py

```diff
 284 | if os.name == "posix":
 285 |     tests.append("symlink")
 286 |+     tests.append("link")
```

> Username: KindDragon  
> Created_at: 2017-03-20 02:22:52 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106828137  

This test works on Windows for me, but not on AppVeyor

> Username: refack  
> Created_at: 2017-03-20 03:00:59 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106829787  

Fails for me locally as well. I opened #185, will look into it soon.


---

## Review 15 [Commented]

> Username: KindDragon  
> Created_at: 2017-03-20 02:48:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/179#pullrequestreview-27768827  

📁 src/engine/guess_toolset.bat

```diff
  46 |+ IF "_%~1_"=="__" goto :eof
  47 |+ set "BOOST_JAM_TOOLSET_ROOT=%~1\VC\"
  48 |+ for /f "tokens=*" %%A in ('cmd /D /S /C "%vsCOMhelper_cmd% VCToolsVersionCode"') do if NOT "_%%A_"=="__" (
```

> Username: KindDragon  
> Created_at: 2017-03-20 02:48:14 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106829240  

Who will check version from `VCToolsVersionCode`?

> Username: refack  
> Created_at: 2017-03-20 02:59:14 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106829714  

So it's forward compatible. Bootstrap will fail with `Unknown toolset: %BOOST_JAM_TOOLSET%`

> Username: KindDragon  
> Created_at: 2017-03-20 03:07:38 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106830049  

I just can't find where get_key_helper.cmd checking `VCToolsVersionCode`

> Username: refack  
> Created_at: 2017-03-20 12:55:31 UTC  
> Updated_at: 2017-03-20 12:55:59 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106892942  

The full command will be `"%~dp0..\tools\vsCOMhelper\get_key_helper.cmd" IsVcCompatible VCToolsVersionCode`  
first arg `IsVcCompatible` is [a filter ](https://github.com/boostorg/build/pull/179/files#diff-88a445c97b267cf7aa36e1880e333390R15)  
second arg `VCToolsVersionCode` is[ a projection](https://github.com/boostorg/build/pull/179/files#diff-88a445c97b267cf7aa36e1880e333390R27)


---

## Review 16 [Commented]

> Username: KindDragon  
> Created_at: 2017-03-20 02:51:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/179#pullrequestreview-27768980  

📁 src/engine/config_toolset.bat

```diff
 170 |+ IF DEFINED EXPENSIVE_POWERSHELL2 goto :Skip_VC1410
 171 |+ SET EXPENSIVE_POWERSHELL2=1
 172 |+ SET vsCOMhelper_cmd="%~dp0..\tools\vsCOMhelper\get_key_helper.cmd" "IsVcCompatible,VisualCppToolsVersionMinor=14.10" "InstallationPath"
```

> Username: KindDragon  
> Created_at: 2017-03-20 02:51:58 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106829389  

I can't find where Powershell/CSharp script check this version

> Username: refack  
> Created_at: 2017-03-20 02:59:59 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106829746  

https://github.com/boostorg/build/pull/179/files#diff-88a445c97b267cf7aa36e1880e333390R17

> Username: refack  
> Created_at: 2017-03-20 03:03:55 UTC  
> Updated_at: 2017-03-20 03:03:56 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106829899  

My concept is; `C#` only does marshaling of data from COM, `powershell` does filtering / projecting.

> Username: refack  
> Created_at: 2017-03-20 03:13:17 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106830308  

@KindDragon I took the filtering idea from you, it's a very good idea 👍 I'm using it in all other projects...


---

## Review 17 [Commented]

> Username: KindDragon  
> Created_at: 2017-03-20 03:06:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/179#pullrequestreview-27769626  

📁 src/engine/guess_toolset.bat

```diff
   1 |- @ECHO OFF
   1 |+ @IF NOT DEFINED DEBUG_BATCH @ECHO OFF
```

> Username: KindDragon  
> Created_at: 2017-03-20 03:06:24 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106829989  

I just comment `@echo off` if I need debugging 😄

> Username: refack  
> Created_at: 2017-03-20 03:07:16 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106830031  

It goes well with https://github.com/boostorg/build/pull/179/files#diff-180360612c6b8c4ed830919bbb4dd459R14

> Username: refack  
> Created_at: 2017-03-20 03:10:34 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106830194  

BTW: stole the concept from VS2017 `VsDevCmd.bat` line 3: `@if NOT "%VSCMD_DEBUG%" GEQ "3" @echo off`


---

## Review 18 [Commented]

> Username: KindDragon  
> Created_at: 2017-03-20 03:27:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/179#pullrequestreview-27770766  

📁 appveyor.yml

```diff
  37 |-     if ($TestExitCode -ne 0) { $host.SetShouldExit($TestExitCode) }
  27 |+     
  28 |+     python test_all.py msvc $env:TEST_ALL_EXTRAS --preserve
```

> Username: KindDragon  
> Created_at: 2017-03-20 03:27:42 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106831146  

It should begin with &

> Username: refack  
> Created_at: 2017-03-20 13:00:34 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106894114  

Not sure why? This works (as it should).  
  
P.S. __&__ operator does not parse variables.  
From the docs:  
  
__&__ Call operator  
==  
Run a command, script, or script block.  
The call operator, also known as the "invocation operator," lets you run commands that are stored in variables and represented by strings.  
__Because the call operator does not parse the command, it cannot interpret command parameters.__  
  
```  
C:\PS> $c = "get-executionpolicy"  
C:\PS> $c  
get-executionpolicy  
C:\PS> & $c  
AllSigned  
```

> Username: KindDragon  
> Created_at: 2017-03-20 15:11:55 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106926691  

You right

> Username: refack  
> Created_at: 2017-03-20 16:20:00 UTC  
> Updated_at: 2017-03-20 16:23:16 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106946156  

http://stackoverflow.com/questions/42382951/whats-the-command-of-call-operator  
Learned something new: You need the __&__ if you want to run a command that's not in env:Path with a space in it's name, i.e.:  
```  
& "C:\bin\dev\VS\SDKs\10\bin\x64\rc.exe" $param1 $param2 $param3  
```

> Username: refack  
> Created_at: 2017-03-20 16:22:53 UTC  
> Url: https://github.com/boostorg/build/pull/179#discussion_r106946911  

I already know you can use it to run `{ script blocks }`  
```  
$reusable = { msbuild "node.sln" /verylong:parameter1 /verylong:parameter2 } //This one doesn't run  
& $reusable 'nobuild';  
& $reusable 'build';  
& $reusable 'test';


---

## Comment 19

> Username: refack  
> Created_at: 2017-04-20 21:49:16 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-295929773  

Made obsolete by official install of `vswhere`

---

## Comment 20

> Username: AndrewPardoe  
> Created_at: 2017-04-20 22:41:41 UTC  
> Url: https://github.com/boostorg/build/pull/179#issuecomment-295949145  

The officially supported vswhere tool is now available as part of the install starting today with Visual Studio 15.2 preview 2: https://blogs.msdn.microsoft.com/heaths/2017/04/21/vswhere-is-now-installed-with-visual-studio-2017/. This doesn't fix all the existing installs of VS 2017 but going forward you can now detect the VS install locations and whether C++ tools are available from a scripted environment. I hope this helps your scenario.

---
