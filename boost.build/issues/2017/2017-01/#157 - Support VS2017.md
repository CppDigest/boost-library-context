# #157 - Support VS2017 [Closed]

> Username: KindDragon  
> Created at: 2017-01-31 01:03:51 UTC  
> Updated at: 2023-12-07 03:04:53 UTC  
> Closed at: 2017-03-12 14:16:39 UTC  
> Url: https://github.com/boostorg/build/issues/157  

This patch https://github.com/boostorg/build/commit/ae5e63a1315493dfb2fc60c4d537674fa637f898 doesn't work with VS2017RC1   
  
See also [Finding the location of a VC++2017 install](https://blogs.msdn.microsoft.com/vcblog/2016/10/07/compiler-tools-layout-in-visual-studio-15/)  
  
BTW toolset should be vc141 for VS2017 and %VSxxxCOMNTOOLS% environment variable doesn't exist

---

## Comment 1

> Username: KindDragon  
> Created at: 2017-02-08 13:20:08 UTC  
> Updated at: 2017-02-08 13:23:08 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-278326691  

How execute command `powershell -v 3 -exec bypass .\return-msvc-path.ps1 15` in jam file (msvc.jam) and get their output and exitcode?

---

## Comment 2

> Username: KindDragon  
> Created at: 2017-02-08 13:26:44 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-278328072  

Script: https://github.com/KindDragon/build/commit/c03c11cc338ba2dad9be6f4aaec74fd3212dec84#diff-edcbda45192bb7d102a378db2cabd8f3

---

## Comment 3

> Username: MarcelRaad  
> Created at: 2017-02-15 21:10:24 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280139711  

> %VSxxxCOMNTOOLS% environment variable doesn't exist  
  
`%VS150COMNTOOLS%` does exist if you use one of the Visual Studio command prompts.

---

## Comment 4

> Username: KindDragon  
> Created at: 2017-02-15 21:18:47 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280142011  

Yes, but for previous versions VS this was not required. Not convenient to run the VS command prompts before Boost.Build

---

## Comment 5

> Username: AndrewPardoe  
> Created at: 2017-02-16 03:18:47 UTC  
> Updated at: 2017-02-16 23:52:00 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280219542  

There are three solutions to this problem:  
  
1. Store a small executable in your repo. Run that executable to locate VS installs. You can identify the latest VS install, and ascertain that it contains MSVC (as opposed to a C#-only install.) We can help you create the optimal (as in, smallest size and specific operation) executable.   
  
2. Use a PowerShell module. This offers the same capability as option 1, but requires that you install a PS module on the user's machine. Worse, I think that the install is interactive (though non-admin.) On Win10 it's straightforward, on earlier versions it's more difficult to install PS modules. More details here: https://blogs.msdn.microsoft.com/heaths/2017/01/25/visual-studio-setup-powershell-module-available/, invoking the command as so: `Get-VSSetupInstance | Select-VSSetupInstance -Latest -Require Microsoft.VisualStudio.Component.VC.Tools.x86.x64`   
  
3. Brute force it through a command script. The following command script searches every single-lettered drive (whether or not it exists) for a file called vcvars64.bat. It calls every vcvars64 it finds. It then checks to make sure it can find a cl.exe. It's unholy and I'm ashamed to have written it but it may suit your purposes:  
  
```  
for %%i in (a b c d e f g h i j k l m n o p q r s t u v w x y z) do for /F "tokens=*" %%j in ('dir /s /b %%i:\vcvars64.bat') do call "%%j"   
call where cl.exe && (echo success) || (echo cl.exe not found)  
```  
  
Edit: Here's a version suggested by a colleague that will call the first vcvars64.bat and exit.  
  
```  
for %%i in (a b c d e f g h i j k l m n o p q r s t u v w x y z) do for /F "tokens=*" %%j in ('dir /s /b %%i:\vcvars64.bat') do (  
    call "%%j"  
    call where cl.exe && goto :EOF   
)  
```  
  
Note that you can change the 64 to a 32 or even call vcvarsall.bat.

---

## Comment 6

> Username: KayEss  
> Created at: 2017-02-16 03:27:39 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280220729  

I'm so pleased I'm not at the pointy end of making a decision about how to do this :(  
  
Shouldn't there be some mechanism for finding the default compiler that the platform provides? After all this must come up for every single eco system that also relies on native code extensions: node, python, ruby etc.

---

## Comment 7

> Username: AndrewPardoe  
> Created at: 2017-02-16 03:33:53 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280221537  

There should be. And I'm trying to get a better solution introduced. What seems to be ideal to me is that we'd ship a binary (similar to option 1 above) that prints the path and sets an environment variable for the last-installed version of VS that contains the MSVC workload. I can't guarantee success but  I can guarantee I'll try to help y'all get through this.

---

## Comment 8

> Username: teeks99  
> Created at: 2017-02-16 03:36:49 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280221885  

> Store a small executable in your repo. Run that executable to locate VS installs.  
  
How does it do this under the hood? Does it do a full search of every drive path? I doubt it. I've read that there isn't a single registry key to query, but are there multiple that we could use to throw together a search path?

---

## Comment 9

> Username: AndrewPardoe  
> Created at: 2017-02-16 03:52:22 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280223707  

Oh, goodness no. It calls a COM API that lists all VS installs. Once you've got an install instance you can query for which workloads are installed, which is the most recent install, etc.   
  
The main code repo is here: https://github.com/microsoft/vs-setup-samples  
  
I hacked up a simplified, command-line compilable version here: https://gist.github.com/AndrewPardoe/975a821aa3e865cfaf576fafbfdcbe2f  
  
That said, we'd want to write a custom version that suited your scenario.

---

## Comment 10

> Username: teeks99  
> Created at: 2017-02-16 12:17:56 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280315808  

And how does the service behind that COM interface know/determine where the installs are? It can't be turtles all the way down.  
  
If there isn't a better solution than 1-3, I propose that we basically leave things as-is for the build.bat side. By supporting the three default paths we will hit 99% of the users. Then for the remaining 1% that don't use the default install path, and we don't detect, on windows we can output a message saying "we weren't able to detect a compiler, if you have VS 2017 installed to a non-default path, you must set the %VS15COOMNTOOLS% variable before calling build.bat/bootstrap.bat".   
  
Once we find any compiler to build the b2 tool, we can have the tool actually query the COM API to figure out the full installed versions and set the appropriate variables. Not being a b2 expert, I don't know if this is possible, I believe it is all done in jam scripts now...is there a avenue to get something like this inside the binary? Would that be normal operating procedure or a terrible hack?  
  
P.S. Forcing users to query a COM API does not help skeptical users buy in to the concept that Microsoft is getting better about supporting industry standards.

---

## Comment 11

> Username: teeks99  
> Created at: 2017-02-16 12:48:42 UTC  
> Updated at: 2017-02-16 12:50:20 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280321673  

There's also the registry key:  
`HKLM\Software\Classes\.cpp\OpenWithProgids`  
  
This should contain a key for all the programs that are setup to open CPP files, Visual Studio registers itself as one of these: `VisualStudio.cpp.53c3cca0`  
  
Going to that location in the registry:  
`HKLM\Software\Classes\[VisualStudio.cpp.53c3cca0]`  
  
Allows you to find:  
`HKLM\Software\Classes\[VisualStudio.cpp.53c3cca0]\shell\Open\Command Default` with the value: `"C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenev.exe" /dde`  
  
Presumably with some nice batch file trimming, this path could be traversed in build.bat. I still need to test with multiple versions and see what that turns into, presumably they'll all be there, and we can just pick the first one that has a 2017 in it...or maybe  not if they're installed into separate dirs.

---

## Comment 12

> Username: KindDragon  
> Created at: 2017-02-16 13:23:26 UTC  
> Updated at: 2017-02-16 13:23:38 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280329033  

> P.S. Forcing users to query a COM API does not help skeptical users buy in to the concept that Microsoft is getting better about supporting industry standards.  
  
VS only for Windows. COM API is industry standard for Windows

---

## Comment 13

> Username: KindDragon  
> Created at: 2017-02-16 13:26:33 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280329699  

> 2. Use a PowerShell module. This offers the same capability as option 1, but requires that you install a PS module on the user's machine.  
  
We can also distribute PowerShell module with Boost.Build, and use script like my https://github.com/KindDragon/build/commit/c03c11cc338ba2dad9be6f4aaec74fd3212dec84#diff-edcbda45192bb7d102a378db2cabd8f3

---

## Comment 14

> Username: eldiener  
> Created at: 2017-02-16 14:17:08 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280341517  

I do not think you want to distribute PowerShell with Boost Build, any more than you want to distribute Python, or some C++ compiler with Boost Build.

---

## Comment 15

> Username: AndrewPardoe  
> Created at: 2017-02-16 15:06:15 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280355572  

PowerShell is included in Windows 7 and up. It's essentially a different command shell with more scripting ability. Please, all, remember we are only talking about Windows here.   
  
@KindDragon, if you opt to use a PowerShell module it does pretty much everything for you with regards to detecting the installed VS builds and their workloads. You shouldn't need any more detection logic.

---

## Comment 16

> Username: refack  
> Created at: 2017-02-17 02:44:15 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-280533645  

I have written [a few scripts](https://github.com/node4good/get-vs2017-path) that use only builtin windows tools to find the path for VS2017 installation and `VsDevCmd.bat`'s location

---

## Comment 17

> Username: AndrewPardoe  
> Created at: 2017-02-26 00:27:04 UTC  
> Updated at: 2017-02-26 00:27:16 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282522950  

FWIW, Heath Stewart has made another sample available on how to locate VS: https://blogs.msdn.microsoft.com/heaths/2017/02/25/vswhere-available/

---

## Comment 18

> Username: KindDragon  
> Created at: 2017-02-26 06:22:36 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282536168  

Cool! Now, if someone suggested how to call it from Boost Jam file would have been nice 😄

---

## Comment 19

> Username: teeks99  
> Created at: 2017-02-26 12:46:05 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282553418  

It doesn't sound like this utility will ship with visual studio, so I'm not sure how it will really help us. I don't think the community will be very excited about packaging it with the source distribution and I don't think we should download it during the script execution.   
  
It still sounds to me like the best idea is to make our own PowerShell script that does the same basic function as that program. I'm not 100% sure that is possible though, most of the examples I've seen have still depended on another binary.

---

## Comment 20

> Username: AndrewPardoe  
> Created at: 2017-02-26 16:58:49 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282569804  

This tool does not ship with Visual Studio currently. I've asked that the team consider deploying it with the VC++ workload but there's nothing to report on that front yet.   
  
You own your own build system: maybe the logic can be incorporated in that binary? I'm thinking of something similar to what @ras0219-msft did with VCPkg.

---

## Comment 21

> Username: teeks99  
> Created at: 2017-02-26 23:08:45 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282596458  

Yeah, the logic could definitely be incorporated in the binary...the real issue is how to do the bootstrapping to build the binary. That is just a .bat file, it just needs to find *any* working compiler so that it can build the tool.

---

## Comment 22

> Username: KindDragon  
> Created at: 2017-02-27 03:45:47 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282621521  

We can just push exe file to the repository or download it from github when bootstrapping Boost.Build.

---

## Comment 23

> Username: KayEss  
> Created at: 2017-02-27 04:02:36 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282623115  

That opens a can of worms. Who would be responsible for the integrity of the file? Who would sign it? How would it work when the next compiler is released?  
  
What are the implications to the user experience of converting the BAT file to a powershell script? Who does it leave needing to download and install extra things?

---

## Comment 24

> Username: teeks99  
> Created at: 2017-02-27 04:12:23 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282624027  

@KindDragon We can technically push the exe file to the git repo, but should we? This seems like it needs to be answered by the larger community beyond those on this issue.

---

## Comment 25

> Username: KindDragon  
> Created at: 2017-02-27 04:15:31 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282624286  

> That opens a can of worms. Who would be responsible for the integrity of the file? Who would sign it? How would it work when the next compiler is released?  
  
I think downloading from official Microsoft repository and checking checksum would be enough  
  
> What are the implications to the user experience of converting the BAT file to a powershell script? Who does it leave needing to download and install extra things?  
  
What BAT file?

---

## Comment 26

> Username: teeks99  
> Created at: 2017-02-27 04:16:01 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282624322  

@KayEss I don't think we can convert the build.bat file to powershell that would break it for anyone who doesn't have powershell and wants an old compiler to run. We still support XP + Visual Studio 2003, no powershell involved there.    
  
Instead, the option would be that when we hit the VS2017 check, we first check to see if powershell is installed, if it is we run a script similar to that vswhere app (is this possible to do in powershell???, I believe this is TBD) which identifies available versions and returns them to the batch file. Similar to how we call the reg tool to find previous versions of visual studio. I think it is reasonable to assume that anyone with VS2017 also has powershell on their system. It comes with win7+ right?

---

## Comment 27

> Username: teeks99  
> Created at: 2017-02-27 04:19:00 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282624600  

> What BAT file?  
  
There are two problems that need to be solved here:  
  
1.  We need to be able to bootstrap the build system, this requires finding an installed compiler, even if the only installed compiler is VS2017. This is what currently uses the build.bat script.  
  
2. We need to have the bjam tool be able to handle the toolset=msvc-15.0 case when building source code.  
  
Maybe we should split these two into separate issues? It seems like we may be able to copy the vswhere functionality inside bjam for the second option.

---

## Comment 28

> Username: KayEss  
> Created at: 2017-02-27 04:20:15 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282624754  

> What BAT file?  
  
The one that bootstraps bjam  
  
> Instead, the option would be that when we hit the VS2017 check....  
  
@teeks99 That sounds (to me at least) like it should work for people.

---

## Comment 29

> Username: KindDragon  
> Created at: 2017-02-27 04:28:37 UTC  
> Updated at: 2017-02-27 04:29:20 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282625507  

> Instead, the option would be that when we hit the VS2017 check, we first check to see if powershell is installed, if it is we run a script similar to that vswhere app (is this possible to do in powershell???  
  
I think it's impossible to detect Vs2017 path from Powershell (https://github.com/Microsoft/vssetup.powershell/releases/tag/1.0.36-rc) or Python without additional dll's.

---

## Comment 30

> Username: KindDragon  
> Created at: 2017-02-27 04:40:37 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282626610  

> There are two problems that need to be solved here:  
>   
> We need to be able to bootstrap the build system, this requires finding an installed compiler, even if the only installed compiler is VS2017. This is what currently uses the build.bat script.  
>   
> We need to have the bjam tool be able to handle the toolset=msvc-15.0 case when building source code.  
  
For first case we can at least run manually `VsDevCmd.bat` before executing build.bat or define `%VS150COMNTOOLS%`. I am more interested in the second problem.

---

## Comment 31

> Username: refack  
> Created at: 2017-02-28 00:37:15 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282903962  

@KindDragon May I remind ya'll that in my package [`windows-autoconf`](https://github.com/node4good/windows-autoconf) there is a powershell script that calls the COM with no exe needed. I can whip up a PR with it.

---

## Comment 32

> Username: KindDragon  
> Created at: 2017-02-28 01:14:56 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282910448  

@refack Your script is cool, but it JIT compiles `GetVS2017Configuration.cs`. It is almost the same for me

---

## Comment 33

> Username: refack  
> Created at: 2017-02-28 01:16:40 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282910750  

At least it's transperant, and maintaiable. No black box `exe`

---

## Comment 34

> Username: teeks99  
> Created at: 2017-02-28 02:13:47 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282920607  

@refack That's exactly what I was talking about to pipe into the build.bat lookup. The C# compiler is built in to every version of powershell (I believe) so this wouldn't be dependent on any external tools, just that one C# source file/script.  
  
For the compiled version in the build tool we can lift the C++ directly from the vswhere tool. I don't think licensing should be an issue there (they both are MIT).

---

## Comment 35

> Username: refack  
> Created at: 2017-02-28 02:17:55 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282921254  

@teeks99 my C# is .NET 2.0 compatible, so it is compatible with every `powershell` since windows 7 SP1.  
My package can do few other things, like make sure that VCTtools is installed, and find MSBuild, etc. I'm working on making it as much as `autoconf` as possible for window platforms.

---

## Comment 36

> Username: refack  
> Created at: 2017-02-28 02:19:32 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282921493  

Forgive my noobiness, but does the Boost eco-system have a package repository? I could make `windows-autoconf` compatible to it (like I did for `npm` and `nuget`, and maybe `pypi`)

---

## Comment 37

> Username: grafikrobot  
> Created at: 2017-02-28 02:50:02 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282926091  

On Mon, Feb 27, 2017 at 8:13 PM, Tom Kent <notifications@github.com> wrote:  
>  
> For the compiled version in the build tool we can lift the C++ directly  
> from the vswhere tool. I don't think licensing should be an issue there  
> (they both are MIT).  
>  
The b2 tool is not MIT. Hence license is an issue.

---

## Comment 38

> Username: grafikrobot  
> Created at: 2017-02-28 02:51:17 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282926285  

On Mon, Feb 27, 2017 at 8:19 PM, Refael Ackermann <notifications@github.com>  
wrote:  
  
> Forgive my noobiness, but does the Boost eco-system have a package  
> repository? I could make windows-autoconf compatible to it (like I did  
> for npm and nuget, and maybe pypi)  
>  
  
It doesn't have a package repo. Not sure what you mean by wanting that  
either.  
  
  
--   
-- Rene Rivera  
-- Grafik - Don't Assume Anything  
-- Robot Dreams - http://robot-dreams.net  
-- rrivera/acm.org (msn) - grafikrobot/aim,yahoo,skype,efnet,gmail

---

## Comment 39

> Username: KindDragon  
> Created at: 2017-02-28 07:44:34 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-282966321  

The main issue is how to call Powershell script from [msvc.jam](https://github.com/boostorg/build/blob/ae5e63a1315493dfb2fc60c4d537674fa637f898/src/tools/msvc.jam#L700-L738)

---

## Comment 40

> Username: refack  
> Created at: 2017-02-28 17:06:24 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-283101294  

No chance of convining you all to move to `ninja` 😜  
I'll dig in `jam` docs and code...

---

## Comment 41

> Username: refack  
> Created at: 2017-03-02 16:01:02 UTC  
> Updated at: 2017-03-02 16:02:14 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-283694570  

@KindDragon who can iluminate me about the `jam` exec and building of `jam` and it's "`Jambase`" configuration process?  
I got it to bootstrap on my machine (MSVS 2017RC only) but the "`Jambase`" doesn't update. I see a few `.py` files, any idea when are they triggered?

---

## Comment 42

> Username: swatanabe  
> Created at: 2017-03-02 16:15:04 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-283698772  

AMDG  
  
On 03/02/2017 09:01 AM, Refael Ackermann wrote:  
> @KindDragon who can iluminate me no `jam` and the `jam` build process.  
> I got it to bootstrap on my machine (MSVS 2017RC only) but the "`Jambase`" doesn't update. I see a few `.py` files, and idea when are they triggered?  
>   
  
?  
  
You shouldn't need to modify Jambase.  
  
In Christ,  
Steven Watanabe

---

## Comment 43

> Username: refack  
> Created at: 2017-03-02 18:31:45 UTC  
> Updated at: 2017-03-02 18:32:35 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-283738342  

Yes yes I know, that's why is double qouted it. I meen the collection of `.jam` files that setup the base rules. Such as: https://github.com/boostorg/build/blob/develop/src/tools/msvc.py#L1296 and it relation to https://github.com/boostorg/build/blob/develop/src/tools/msvc.jam#L1627  
  
When is https://github.com/boostorg/build/blob/develop/src/tools/msvc.py used?

---

## Comment 44

> Username: refack  
> Created at: 2017-03-02 18:33:48 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-283738900  

meanwhile   
![image](https://cloud.githubusercontent.com/assets/96947/23521407/dc4aa4f0-ff4c-11e6-90f7-96a86452c0af.png)

---

## Comment 45

> Username: swatanabe  
> Created at: 2017-03-02 18:53:44 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-283744263  

AMDG  
  
On 03/02/2017 11:31 AM, Refael Ackermann wrote:  
> Yes yes I know, that's why is double qouted it. I meen the collection of `.jam` files that setup the base rules. Such as: https://github.com/boostorg/build/blob/develop/src/tools/msvc.py#L1296 and it relation to https://github.com/boostorg/build/blob/develop/src/tools/msvc.jam#L1627  
>   
  
  Ignore the python files.  They're an (incomplete)  
alternate implementation that is not used by default.  
  
In Christ,  
Steven Watanabe

---

## Comment 46

> Username: refack  
> Created at: 2017-03-02 19:05:06 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-283747474  

1. Shame, because they allow me to spawn a shell and run a `.cmd` file. Any way to do that via the `.jam`s?  
2. Another option is to manipulate `msvs.jam` during bootstrap, or would that be gauche?  
3. We could leave a cookie-crumb in the registry (as `jam` can access the registry) during bootstrap.

---

## Comment 47

> Username: swatanabe  
> Created at: 2017-03-02 19:19:18 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-283751361  

AMDG  
  
On 03/02/2017 12:05 PM, Refael Ackermann wrote:  
> 1. Shame, because they allow me to spawn a shell and run a `.cmd` file. Any way to do that via the `.jam`s?  
  
[ SHELL $(command) ]  
  
> 2. Another option is to manipulate `msvs.jam` during bootstrap, or would that be gauche?  
  
  Running build.bat must be sufficient to use  
Boost.Build inside the Boost tree and must  
not do anything except build b2.exe.  
  
> 3. We could leave a cookie-crumb in the registry (as `jam` can access the registry) during bootstrap.  
>   
  
Please no.  
  
In Christ,  
Steven Watanabe

---

## Comment 48

> Username: refack  
> Created at: 2017-03-02 23:49:37 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-283820442  

Thanks

---

## Comment 49

> Username: KindDragon  
> Created at: 2017-03-03 02:49:12 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-283850308  

@swatanabe Thank you

---

## Comment 50

> Username: refack  
> Created at: 2017-03-03 17:09:28 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-284012005  

Done #170 (don't know why it dragged so many landed commits with it)

---

## Comment 51

> Username: refack  
> Created at: 2017-03-05 17:45:07 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-284246381  

> Done #170 (don't know why it dragged so many landed commits with it)  
  
found out way 🤦

---

## Comment 52

> Username: juandent  
> Created at: 2017-03-09 17:11:40 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285415845  

I just installed Visual Studio 2017 final release, but have not been able to build Boost 1.63 with it... It defaults to using Visual Studio 2015 update 3... I understand there is currently no support for VS 2017, is this correct? If so, are we very far behind for getting this to work with VS 2017??

---

## Comment 53

> Username: grafikrobot  
> Created at: 2017-03-09 17:17:03 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285417321  

I'm working to verify the two PRs for getting b2 working with it. So.. soon. Likely not today though.

---

## Comment 54

> Username: juandent  
> Created at: 2017-03-09 17:47:27 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285426069  

Great!! thanks for your effort!!

---

## Comment 55

> Username: jhunold  
> Created at: 2017-03-10 19:12:43 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285758084  

The detection logic in  `cl141_path.cmd` does not work for the default installion in `C:\Program Files ...`.  
The `vc141_path.cmd` just reports  `C:\Program` so it has problems with the white space in the path.

---

## Comment 56

> Username: refack  
> Created at: 2017-03-10 19:14:45 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285758618  

@jhunold literally just fixed it :)

---

## Comment 57

> Username: jhunold  
> Created at: 2017-03-10 19:34:43 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285763607  

This issue, yes. Thanks. Unfortunately, the full path including `cl.exe` is then passed to `register-configuration` where the `GLOB`then fails. Working on the path magic...

---

## Comment 58

> Username: refack  
> Created at: 2017-03-10 19:35:44 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285763853  

grrrrrr

---

## Comment 59

> Username: refack  
> Created at: 2017-03-10 19:41:22 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285765318  

@jhunold that's why on my dev machine VS is installed in a spaceless path 🤓  
Testing on a new VM, and adding `boostorg/build` to my CI [![Build status](https://ci.appveyor.com/api/projects/status/u7812xmwxij7ljlh?svg=true)](https://ci.appveyor.com/project/refack/windows-autoconf)

---

## Comment 60

> Username: jhunold  
> Created at: 2017-03-10 19:44:35 UTC  
> Updated at: 2017-03-10 19:46:21 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285766108  

Patch for selecting the directory only in https://gist.github.com/jhunold/8fe9d13c536a0954faa6c2152c24e562  
Default installation is unfortunately quite common...

---

## Comment 61

> Username: refack  
> Created at: 2017-03-10 23:05:20 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285809535  

@jhunold patched... Was way harder then should have. `cmd` if so fickle.

---

## Comment 62

> Username: refack  
> Created at: 2017-03-12 15:12:40 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285951012  

big thanks 🥇 to @swatanabe   
also thanks @jhunold for pushing me for more extensive testing

---

## Comment 63

> Username: juandent  
> Created at: 2017-03-12 17:41:43 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285960685  

Hi Rene!,  
  
   
  
Why is the case closed? Are you any closer to solving support for building Boost with Visual Studio 2017?  
  
   
  
   
  
Regards,  
  
Juan Dent  
  
   
  
From: Rene Rivera [mailto:notifications@github.com]   
Sent: Thursday, March 9, 2017 11:17 AM  
To: boostorg/build <build@noreply.github.com>  
Cc: juandent <juandent@mac.com>; Comment <comment@noreply.github.com>  
Subject: Re: [boostorg/build] Support VS2017 (#157)  
  
   
  
I'm working to verify the two PRs for getting b2 working with it. So.. soon. Likely not today though.  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/build/issues/157#issuecomment-285417321> , or mute the thread <https://github.com/notifications/unsubscribe-auth/AFHnFVuxGRzut9iLX--h7Fh6XLxGM2Kbks5rkDQQgaJpZM4LyHmS> .  <https://github.com/notifications/beacon/AFHnFUrGWqY2QbaNbLc7mhUDtfJwRUomks5rkDQQgaJpZM4LyHmS.gif>

---

## Comment 64

> Username: grafikrobot  
> Created at: 2017-03-12 18:33:44 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285964198  

I merged, or manually applied, all the build changes to develop branch for  
vs2017. Haven't had time to merge to master yet though.  
  
On Mar 12, 2017 12:41 PM, "juandent" <notifications@github.com> wrote:  
  
Hi Rene!,  
  
  
  
Why is the case closed? Are you any closer to solving support for building  
Boost with Visual Studio 2017?  
  
  
  
  
  
Regards,  
  
Juan Dent  
  
  
  
From: Rene Rivera [mailto:notifications@github.com]  
Sent: Thursday, March 9, 2017 11:17 AM  
To: boostorg/build <build@noreply.github.com>  
Cc: juandent <juandent@mac.com>; Comment <comment@noreply.github.com>  
Subject: Re: [boostorg/build] Support VS2017 (#157)  
  
  
  
I'm working to verify the two PRs for getting b2 working with it. So..  
soon. Likely not today though.  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub <  
https://github.com/boostorg/build/issues/157#issuecomment-285417321> , or  
mute the thread <https://github.com/notifications/unsubscribe-  
auth/AFHnFVuxGRzut9iLX--h7Fh6XLxGM2Kbks5rkDQQgaJpZM4LyHmS> . <  
https://github.com/notifications/beacon/AFHnFUrGWqY2QbaNbLc7mhUDtfJwRU  
omks5rkDQQgaJpZM4LyHmS.gif>  
  
—  
You are receiving this because you modified the open/close state.  
  
Reply to this email directly, view it on GitHub  
<https://github.com/boostorg/build/issues/157#issuecomment-285960685>, or mute  
the thread  
<https://github.com/notifications/unsubscribe-auth/AC2_7QOWd7R8CtcYhlCDPs-MLw7rCOIDks5rlC5YgaJpZM4LyHmS>  
.

---

## Comment 65

> Username: juandent  
> Created at: 2017-03-12 22:51:52 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-285983907  

Si all i need to do is download the develop branch?  
Is this for boost 1.63?  
  
Regards  
Juan dent  
  
Sent from my iPhone  
  
> On Mar 12, 2017, at 12:33 PM, Rene Rivera <notifications@github.com> wrote:  
>   
> I merged, or manually applied, all the build changes to develop branch for  
> vs2017. Haven't had time to merge to master yet though.  
>   
> On Mar 12, 2017 12:41 PM, "juandent" <notifications@github.com> wrote:  
>   
> Hi Rene!,  
>   
>   
>   
> Why is the case closed? Are you any closer to solving support for building  
> Boost with Visual Studio 2017?  
>   
>   
>   
>   
>   
> Regards,  
>   
> Juan Dent  
>   
>   
>   
> From: Rene Rivera [mailto:notifications@github.com]  
> Sent: Thursday, March 9, 2017 11:17 AM  
> To: boostorg/build <build@noreply.github.com>  
> Cc: juandent <juandent@mac.com>; Comment <comment@noreply.github.com>  
> Subject: Re: [boostorg/build] Support VS2017 (#157)  
>   
>   
>   
> I'm working to verify the two PRs for getting b2 working with it. So..  
> soon. Likely not today though.  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub <  
> https://github.com/boostorg/build/issues/157#issuecomment-285417321> , or  
> mute the thread <https://github.com/notifications/unsubscribe-  
> auth/AFHnFVuxGRzut9iLX--h7Fh6XLxGM2Kbks5rkDQQgaJpZM4LyHmS> . <  
> https://github.com/notifications/beacon/AFHnFUrGWqY2QbaNbLc7mhUDtfJwRU  
> omks5rkDQQgaJpZM4LyHmS.gif>  
>   
> —  
> You are receiving this because you modified the open/close state.  
>   
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/build/issues/157#issuecomment-285960685>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AC2_7QOWd7R8CtcYhlCDPs-MLw7rCOIDks5rlC5YgaJpZM4LyHmS>  
> .  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub, or mute the thread.  
>

---

## Comment 66

> Username: KindDragon  
> Created at: 2017-03-13 13:59:42 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-286114739  

Download develop branch and patch `boost\config\auto_link.hpp`

---

## Comment 67

> Username: juandent  
> Created at: 2017-03-17 18:26:47 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287434942  

Hi @KindDragon, I downloaded the develop branch but there is no auto_link.hpp in that download anywhere so I do not get what "patch .."means. Also, am I to copy all the files in the develop branch on top of the boost_63_0 files? Is that how the patching takes place?  
  
Forgive me if this is very simple, but I am relatively new to GitHub.  
Also, is all this to work with the final release of VS2017? (i.e. not with release candidates)?  
  
Thanks  
Juan

---

## Comment 68

> Username: juandent  
> Created at: 2017-03-17 18:51:09 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287441204  

Hi,  
  
   
  
I find that Visual Studio 2017 final release has version 15, not 14.1 …will all the work done in this thread will be obsolete and only usable with release candidates?  
  
Another issue: I downloaded the develop branch as suggested but how is the patching done – only copy over existing files and rebuild b2? What about auto_link.hpp? Just #define for vc15?  
  
   
  
Regards,  
  
Juan Dent  
  
   
  
From: Rene Rivera [mailto:notifications@github.com]   
Sent: Sunday, March 12, 2017 12:34 PM  
To: boostorg/build <build@noreply.github.com>  
Cc: juandent <juandent@mac.com>; Comment <comment@noreply.github.com>  
Subject: Re: [boostorg/build] Support VS2017 (#157)  
  
   
  
I merged, or manually applied, all the build changes to develop branch for  
vs2017. Haven't had time to merge to master yet though.  
  
On Mar 12, 2017 12:41 PM, "juandent" <notifications@github.com <mailto:notifications@github.com> > wrote:  
  
Hi Rene!,  
  
  
  
Why is the case closed? Are you any closer to solving support for building  
Boost with Visual Studio 2017?  
  
  
  
  
  
Regards,  
  
Juan Dent  
  
  
  
From: Rene Rivera [mailto:notifications@github.com]  
Sent: Thursday, March 9, 2017 11:17 AM  
To: boostorg/build <build@noreply.github.com <mailto:build@noreply.github.com> >  
Cc: juandent <juandent@mac.com <mailto:juandent@mac.com> >; Comment <comment@noreply.github.com <mailto:comment@noreply.github.com> >  
Subject: Re: [boostorg/build] Support VS2017 (#157)  
  
  
  
I'm working to verify the two PRs for getting b2 working with it. So..  
soon. Likely not today though.  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub <  
https://github.com/boostorg/build/issues/157#issuecomment-285417321> , or  
mute the thread <https://github.com/notifications/unsubscribe- <https://github.com/notifications/unsubscribe-%0bauth/AFHnFVuxGRzut9iLX--h7Fh6XLxGM2Kbks5rkDQQgaJpZM4LyHmS>   
auth/AFHnFVuxGRzut9iLX--h7Fh6XLxGM2Kbks5rkDQQgaJpZM4LyHmS> . <  
https://github.com/notifications/beacon/AFHnFUrGWqY2QbaNbLc7mhUDtfJwRU  
omks5rkDQQgaJpZM4LyHmS.gif>  
  
—  
You are receiving this because you modified the open/close state.  
  
Reply to this email directly, view it on GitHub  
<https://github.com/boostorg/build/issues/157#issuecomment-285960685>, or mute  
the thread  
<https://github.com/notifications/unsubscribe-auth/AC2_7QOWd7R8CtcYhlCDPs-MLw7rCOIDks5rlC5YgaJpZM4LyHmS>  
.  
  
  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/build/issues/157#issuecomment-285964198> , or mute the thread <https://github.com/notifications/unsubscribe-auth/AFHnFdJnLKoPiv4IoHDoY1gvTbBkW0nwks5rlDqKgaJpZM4LyHmS> .  <https://github.com/notifications/beacon/AFHnFVT_FrVKF5bGszpIwpu3Hm6VcnqAks5rlDqKgaJpZM4LyHmS.gif>

---

## Comment 69

> Username: AndrewPardoe  
> Created at: 2017-03-17 19:28:48 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287449765  

>> I find that Visual Studio 2017 final release has version 15, not 14.1 …will all the work done in this thread will be obsolete and only usable with release candidates?  
  
I'm seeing the toolset version in VS 2017 RTW as v141. Where are you getting your information?

---

## Comment 70

> Username: juandent  
> Created at: 2017-03-17 19:31:51 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287450440  

I may be totally wrong - when I open the command prompt that comes with VS 2017 I see:  
  
   
  
Visual Studio 2017 Developer Command Prompt v15.0.26228.4  
  
   
  
Regards,  
  
Juan  
  
   
  
As an aside, how is the patch applied?  
  
   
  
   
  
From: Andrew Pardoe [mailto:notifications@github.com]   
Sent: Friday, March 17, 2017 1:29 PM  
To: boostorg/build <build@noreply.github.com>  
Cc: juandent <juandent@mac.com>; Comment <comment@noreply.github.com>  
Subject: Re: [boostorg/build] Support VS2017 (#157)  
  
   
  
I find that Visual Studio 2017 final release has version 15, not 14.1 …will all the work done in this thread will be obsolete and only usable with release candidates?  
  
I'm seeing the toolset version in VS 2017 RTW as v141. Where are you getting your information?  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/build/issues/157#issuecomment-287449765> , or mute the thread <https://github.com/notifications/unsubscribe-auth/AFHnFZyl6YgA2PWIGGEMjWCmTf7W_hDcks5rmt7xgaJpZM4LyHmS> .  <https://github.com/notifications/beacon/AFHnFW5uK-F3Vz8l2B6XeMvEWGupE0Alks5rmt7xgaJpZM4LyHmS.gif>

---

## Comment 71

> Username: AndrewPardoe  
> Created at: 2017-03-17 19:33:35 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287450790  

You need to run cl -Bv to see the compiler version number. Microsoft's versioning story is a dumpster fire.

---

## Comment 72

> Username: juandent  
> Created at: 2017-03-17 19:39:08 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287452001  

Okay, thanks!  
  
   
  
I get:  
  
   
  
                C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.10.25017\bin\HostX64\x64\cl.exe:        Version 19.10.25017.0  
  
   
  
So II guess its 14.1 since it is definitely not 19.1!  
  
   
  
How do we apply the patch? Do we copy over the existing files on top of the downloaded Boost_13_0?   
  
What about auto_lin.hpp? Do we add  #  define BOOST_LIB_TOOLSET "vc141"?  
  
   
  
Best regards!!  
  
Juan  
  
   
  
From: Andrew Pardoe [mailto:notifications@github.com]   
Sent: Friday, March 17, 2017 1:34 PM  
To: boostorg/build <build@noreply.github.com>  
Cc: juandent <juandent@mac.com>; Comment <comment@noreply.github.com>  
Subject: Re: [boostorg/build] Support VS2017 (#157)  
  
   
  
You need to run cl -Bv to see the compiler version number. Microsoft's versioning story is a dumpster fire.  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/build/issues/157#issuecomment-287450790> , or mute the thread <https://github.com/notifications/unsubscribe-auth/AFHnFVzQ7c0EpbarNIGZg7cWk7XRb-fXks5rmuAQgaJpZM4LyHmS> .  <https://github.com/notifications/beacon/AFHnFbvJyEc-MLv0ORittM-HuzsEFM_Vks5rmuAQgaJpZM4LyHmS.gif>

---

## Comment 73

> Username: AndrewPardoe  
> Created at: 2017-03-17 19:43:46 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287453025  

>> C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.10.25017\bin\HostX64\x64\cl.exe:        Version 19.10.25017.0  
  
The compiler version number is 19.10, which is equivalent to the VS version number of 14.10.   
  
I don't know how to apply the patch. I'm on the MSVC team, not the Boost org.

---

## Comment 74

> Username: refack  
> Created at: 2017-03-17 19:53:47 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287455219  

@juandent I'm not a Boost expert, but you could try downloading `Boost/build` (`develop` branch) than running `bootstrap.bat` and following instructions to install a new version of `bjam`. Then try building `boost` with the new `bjam`

---

## Comment 75

> Username: juandent  
> Created at: 2017-03-17 19:57:31 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287456043  

Thank you!  
  
   
  
So to be completely certain I understand you:  
  
   
  
1.	I must copy from the download over the original boost_63_0?  
2.	The bootstrap.bat contains instructions as to how to patch auto_link.hpp  
  
   
  
Regards,  
  
Juan  
  
   
  
From: Refael Ackermann [mailto:notifications@github.com]   
Sent: Friday, March 17, 2017 1:54 PM  
To: boostorg/build <build@noreply.github.com>  
Cc: juandent <juandent@mac.com>; Mention <mention@noreply.github.com>  
Subject: Re: [boostorg/build] Support VS2017 (#157)  
  
   
  
@juandent <https://github.com/juandent>  I'm not a Boost expert, but you could try downloading Boost/build (develop branch) than running bootstrap.bat and following instructions to install a new version of bjam. Then try building boost with the new bjam  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/build/issues/157#issuecomment-287455219> , or mute the thread <https://github.com/notifications/unsubscribe-auth/AFHnFQyBnT5wWp7Gxpymt6-mGj5deiylks5rmuTMgaJpZM4LyHmS> .  <https://github.com/notifications/beacon/AFHnFUx_KofLDoLBCrTsqhNGAPrwckWVks5rmuTMgaJpZM4LyHmS.gif>

---

## Comment 76

> Username: juandent  
> Created at: 2017-03-17 22:01:04 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287482190  

Hi @refack I did what you suggested:   
  build the new b2.exe and bjam.exe with the downloaded code form the develop branch here, and then copied them to boost_63_0 where I tried calling b2 --show-libraries and got this:  
  
```  
C:\Users\Juan Dent\Documents\GitHub\boost_1_63_0>b2 --show-libraries  
C:/Users/Juan Dent/Documents/GitHub/boost_1_63_0/tools/build/src/tools\msvc.jam:834: in generate-setup-cmd  
*** argument error  
* rule maybe-rewrite-setup ( toolset : setup-script : setup-options : version : rewrite-setup ? )  
* called with: ( msvc :  :  : 14.1 :  )  
* missing argument setup-script  
C:/Users/Juan Dent/Documents/GitHub/boost_1_63_0/tools/build/src/tools\msvc.jam:746:see definition of rule 'maybe-rewrite-setup' being called  
C:/Users/Juan Dent/Documents/GitHub/boost_1_63_0/tools/build/src/tools\msvc.jam:1076: in configure-really  
C:/Users/Juan Dent/Documents/GitHub/boost_1_63_0/tools/build/src/tools\msvc.jam:201: in configure  
C:/Users/Juan Dent/Documents/GitHub/boost_1_63_0/tools/build/src/tools\msvc.jam:153: in msvc.init  
C:/Users/Juan Dent/Documents/GitHub/boost_1_63_0/tools/build/src/build\toolset.jam:43: in toolset.using  
C:/Users/Juan Dent/Documents/GitHub/boost_1_63_0/tools/build/src/build\project.jam:1052: in using  
project-config.jam:3: in modules.load  
C:/Users/Juan Dent/Documents/GitHub/boost_1_63_0/tools/build/src\build-system.jam:249: in load-config  
C:/Users/Juan Dent/Documents/GitHub/boost_1_63_0/tools/build/src\build-system.jam:412: in load-configuration-files  
C:/Users/Juan Dent/Documents/GitHub/boost_1_63_0/tools/build/src\build-system.jam:524: in load  
C:\Users\Juan Dent\Documents\GitHub\boost_1_63_0\tools\build\src/kernel\modules.jam:295: in import  
C:\Users\Juan Dent\Documents\GitHub\boost_1_63_0\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
C:\Users\Juan Dent\Documents\GitHub\boost_1_63_0\boost-build.jam:17: in module scope  
```  
So, I think I need to copy the downloaded files on top of boost_1_63_0 and run b2 from there.  
Does that sound better?  
  
Regards,  
Juan

---

## Comment 77

> Username: refack  
> Created at: 2017-03-17 22:56:34 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287491153  

@juandent I think you'll at least need all the `.jam` files

---

## Comment 78

> Username: juandent  
> Created at: 2017-03-17 22:58:05 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287491352  

Ok, thanks … I will check that  
  
   
  
Juan  
  
   
  
From: Refael Ackermann [mailto:notifications@github.com]   
Sent: Friday, March 17, 2017 4:57 PM  
To: boostorg/build <build@noreply.github.com>  
Cc: juandent <juandent@mac.com>; Mention <mention@noreply.github.com>  
Subject: Re: [boostorg/build] Support VS2017 (#157)  
  
   
  
@juandent <https://github.com/juandent>  I think you'll at least need all the .jam files  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/build/issues/157#issuecomment-287491153> , or mute the thread <https://github.com/notifications/unsubscribe-auth/AFHnFdC1msQ4-V0iljrVNK6ZBR96dVGDks5rmw-jgaJpZM4LyHmS> .  <https://github.com/notifications/beacon/AFHnFUx7DqCVZqM4SNI0BdiQSvraNUcRks5rmw-jgaJpZM4LyHmS.gif>

---

## Comment 79

> Username: KindDragon  
> Created at: 2017-03-18 05:10:29 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287517250  

Clone `boost` from github (https://github.com/boostorg/boost/tree/develop) `develop` branch and try build from it

---

## Comment 80

> Username: juandent  
> Created at: 2017-03-19 00:06:03 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287584296  

Hi @KindDragon I downloaded as you suggested but got this:  
  
```  
C:\Users\Juan Dent\Documents\GitHub\boost-develop>bootstrap.bat  
Building Boost.Build engine  
The system cannot find the path specified.  
Access is denied.  
  
Failed to build Boost.Build engine.  
Please consult bootstrap.log for further diagnostics.  
  
You can try to obtain a prebuilt binary from  
  
   http://sf.net/project/showfiles.php?group_id=7586&package_id=72941  
  
Also, you can file an issue at http://svn.boost.org  
Please attach bootstrap.log in that case.  
```  
  
Should I copy the develop branch on top of Boost_1_63_0?  
  
Or should I copy the boost-build instead on top of Boost_1_63_0?  
  
Regards,  
Juan Dent

---

## Comment 81

> Username: KindDragon  
> Created at: 2017-03-19 05:18:37 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287595260  

`bootstrap.bat` should work, attach bootstrap.log here

---

## Comment 82

> Username: teeks99  
> Created at: 2017-03-19 14:07:39 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287619057  

For those still following this thread, please voice your opinion on the mailing list:  
http://lists.boost.org/Archives/boost/2017/03/233543.php

---

## Comment 83

> Username: refack  
> Created at: 2017-03-19 18:47:07 UTC  
> Updated at: 2017-03-19 18:47:25 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287637735  

> For those still following this thread, please voice your opinion on the mailing list:  
>http://lists.boost.org/Archives/boost/2017/03/233543.php  
I'm not on the list but  
  
👍 for `14.10` & `vc1410`

---

## Comment 84

> Username: KindDragon  
> Created at: 2017-03-20 03:31:47 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287672932  

> For those still following this thread, please voice your opinion on the mailing list:  
> http://lists.boost.org/Archives/boost/2017/03/233543.php  
  
I'm for option two `vc$(PlatformToolsetVersion)`, but I'm fine with option one too

---

## Comment 85

> Username: teeks99  
> Created at: 2017-03-20 11:50:41 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287738254  

@refack and @KindDragon could you guys please go and indicate your preference on the mailing list. As this is a multi-project spanning decision we need to form the consensus there.

---

## Comment 86

> Username: refack  
> Created at: 2017-03-20 12:18:15 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287743309  

We need some MS feedback  
/cc @AndrewPardoe @heaths

---

## Comment 87

> Username: KindDragon  
> Created at: 2017-03-20 15:22:10 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287793201  

> @refack and @KindDragon could you guys please go and indicate your preference on the mailing list. As this is a multi-project spanning decision we need to form the consensus there.  
  
I wasn't subscribed to mailing list

---

## Comment 88

> Username: heaths  
> Created at: 2017-03-20 17:09:43 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287829088  

@refack regarding what, exactly? Is this a deployment issue? From the thread you referenced, it looks more like a VC-related build/reference issue. I can't really help with that more than to direct you at them. If so, I can see if I can find one of them on GitHub and CC them on this thread.

---

## Comment 89

> Username: AndrewPardoe  
> Created at: 2017-03-20 17:18:39 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287831986  

@heaths, this is entirely on me & the MSVC team. I'll pick up the discussion on the Boost mailing list.

---

## Comment 90

> Username: refack  
> Created at: 2017-03-20 20:07:53 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-287881919  

@AndrewPardoe @heaths thank you both!

---

## Comment 91

> Username: refack  
> Created at: 2017-04-13 23:14:59 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-294045247  

@grafikrobot I'm interested to know what was the reason for the regression by 838c6229392c6caf67? Since it's surely not a recursion issue.  
If it's a protest against Microsoft I'm all for it!

---

## Comment 92

> Username: heaths  
> Created at: 2017-04-20 21:02:31 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-295911677  

_vswhere_ is now available as part of the install starting today with Visual Studio 15.2 preview 2: https://blogs.msdn.microsoft.com/heaths/2017/04/21/vswhere-is-now-installed-with-visual-studio-2017/

---

## Comment 93

> Username: DISAPPEARED13  
> Created at: 2023-12-07 03:03:51 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-1844149193  

It seems that boost 1.82 support VS2017 MSVC14.1, but I cannot run Boost.Build with Error C1083 cannot open include file stdint.h, or run it in powershell showing the error C1043, it's quite wired because I can include this file in the IDE, and no error occurs. How to solve this problem? Could you give me some advice!!?? please!

---

## Comment 94

> Username: DISAPPEARED13  
> Created at: 2023-12-07 03:04:52 UTC  
> Url: https://github.com/boostorg/build/issues/157#issuecomment-1844149988  

plus, the toolset is MSVC14.10 like 14.12 / 14.16, it seems that still run with TOOLSET vc141, is it correct? but I cannot run vc142 and vc143, i guess it should be vc141. the TOOLSET should be right.
