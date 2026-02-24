# #401 Support for Visual Studio 2019 (msvc-14.2) [Merged]

> Username: teeks99  
> Created at: 2019-03-01 13:07:10 UTC  
> Updated at: 2021-10-02 21:19:14 UTC  
> Merged at: 2019-03-01 22:45:05 UTC  
> Closed at: 2019-03-01 22:45:05 UTC  
> Url: https://github.com/boostorg/build/pull/401  

This is the smallest of starts to providing VS 2019 support. This fixes the vswhere_usability_wrapper.cmd that is called by build.cmd/guess_toolset.cmd. It doesn't include the fixes that are needed to use the VS160COMNTOOLS env variable.

---

## Comment 1

> Username: teeks99  
> Created_at: 2019-03-01 22:14:40 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-468830278  

With the latest commit, I believe everything is there to build with the RC of VS2019.   
  
See the details in this [gist](https://gist.github.com/teeks99/51e46b19d41ce73e98c691190880e40a).   
  
With this branch, I started on a blank command prompt (not the vs2019 command prompt) and was able to bootstrap then build examples/hello for toolset=msvc-14.2.

---

## Comment 2

> Username: swatanabe  
> Created_at: 2019-03-01 22:25:29 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-468832918  

AMDG  
  
On 3/1/19 3:14 PM, Tom Kent wrote:  
> With the latest commit, I believe everything is there to build with the RC of VS2019.   
>   
> See the details in this [gist](https://gist.github.com/teeks99/51e46b19d41ce73e98c691190880e40a).   
>   
> With this branch, I started on a blank command prompt (not the vs2019 command prompt) and was able to bootstrap then build examples/hello for toolset=msvc-14.2.   
>   
  
Thank you for doing this Tom.  I'll review and  
merge it shortly.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-03-03 11:20:04 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469013028  

After this change, Appveyor with the VS2017 image started picking msvc-14.2 as the autodetected toolset. According to Appveyor's docs, VS2019 is not present on this image, but it's possible they are out of date. This probably needs to be looked into.

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-03-03 13:09:45 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469021387  

According to  
  
https://ci.appveyor.com/project/vinniefalco/beast/build/job/qctviwf9mbustknn#L863  
  
```  
notice: [msvc-cfg] msvc-14.2 detected, command: 'C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.12.25827\bin\HostX64\arm\cl.exe'  
notice: [msvc-cfg] msvc-14.1 detected, command: 'C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.12.25827\bin\HostX64\arm\cl.exe'  
notice: will use 'C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.12.25827\bin\HostX64\arm\cl.exe' for msvc, condition <toolset>msvc-14.2  
```  
  
it finds both 14.1 and 14.2 at the same place.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2019-03-04 22:41:17 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469451545  

AMDG  
  
On 3/3/19 6:09 AM, Peter Dimov wrote:  
> According to  
>   
> https://ci.appveyor.com/project/vinniefalco/beast/build/job/qctviwf9mbustknn#L863  
>   
> ```  
> notice: [msvc-cfg] msvc-14.2 detected, command: 'C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.12.25827\bin\HostX64\arm\cl.exe'  
> notice: [msvc-cfg] msvc-14.1 detected, command: 'C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.12.25827\bin\HostX64\arm\cl.exe'  
> notice: will use 'C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.12.25827\bin\HostX64\arm\cl.exe' for msvc, condition <toolset>msvc-14.2  
> ```  
>   
> it finds both 14.1 and 14.2 at the same place.  
>   
  
I missed this one because I explicitly enumerate  
all my versions of msvc.  Maybe we should require  
vswhere for 14.2 and drop the environmental variable  
fallback.  Also, should it be VS150COMNTOOLS or  
VS160COMNTOOLS?  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-03-04 23:03:03 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469457713  

To be honest I'm not entirely sure why this happens. Is that because of  
  
```  
 2034   +.version-14.2-path =    
  2035   +    "../../VC/Tools/MSVC/*/bin/Host*/*"    
  2036   +    "Microsoft Visual Studio/2017/*/VC/Tools/MSVC/*/bin/Host*/*"    
  2037   +    ;    
  2038   +.version-14.2-env = VS150COMNTOOLS ProgramFiles ProgramFiles(x86) ;    
```  
?  
  
The VS150COMNTOOLS looks like a typo here because in another place of the same patch it's VS160COMNTOOLS.  
  
Also, finding `arm\cl.exe` seems a bit odd, although it works, so it's probably harmless.

---

## Comment 7

> Username: swatanabe  
> Created_at: 2019-03-05 00:09:40 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469479492  

AMDG  
  
On 3/4/19 4:03 PM, Peter Dimov wrote:  
> To be honest I'm not entirely sure why this happens. Is that because of  
>   
> ```  
>  2034   +.version-14.2-path =    
>   2035   +    "../../VC/Tools/MSVC/*/bin/Host*/*"    
>   2036   +    "Microsoft Visual Studio/2017/*/VC/Tools/MSVC/*/bin/Host*/*"    
>   2037   +    ;    
>   2038   +.version-14.2-env = VS150COMNTOOLS ProgramFiles ProgramFiles(x86) ;    
> ```  
> ?  
>   
> The VS150COMNTOOLS looks like a typo here because in another place of the same patch it's VS160COMNTOOLS.  
>   
  
It looks that way to me as well.  
  
> Also, finding `arm\cl.exe` seems a bit odd, although it works, so it's probably harmless.  
>   
  
We don't actually use the compiler path for anything, except  
as a base for finding the setup script.  As such it doesn't  
matter which compiler we find as long as there's at least one.  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: pdimov  
> Created_at: 2019-03-05 01:11:10 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469493323  

Also, is the `2017` on line 2036 correct? Does 2019 install into a directory "2017"?

---

## Comment 9

> Username: swatanabe  
> Created_at: 2019-03-05 01:29:39 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469497421  

AMDG  
  
On 3/4/19 6:11 PM, Peter Dimov wrote:  
> Also, is the `2017` on line 2036 correct? Does 2019 install into a directory "2017"?  
>   
  
Alright.  Here's my proposed path forward:  
  
- If vswhere is found, do not attempt other methods  
  of detecting msvc.  
- Confirm corrections to the environment variables  
  and paths with someone who has the RC.  Tom?  
  
In Christ,  
Steven Watanabe

---

## Comment 10

> Username: teeks99  
> Created_at: 2019-03-05 01:31:54 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469497922  

Yeah, these two are sloppy copy-paste errors  
```  
 2034   +.version-14.2-path =    
  2035   +    "../../VC/Tools/MSVC/*/bin/Host*/*"    
  2036   +    "Microsoft Visual Studio/2017/*/VC/Tools/MSVC/*/bin/Host*/*"    
  2037   +    ;    
  2038   +.version-14.2-env = VS150COMNTOOLS ProgramFiles ProgramFiles(x86) ;    
```  
  
Should be  
```  
 2034   +.version-14.2-path =    
  2035   +    "../../VC/Tools/MSVC/*/bin/Host*/*"    
  2036   +    "Microsoft Visual Studio/2019/*/VC/Tools/MSVC/*/bin/Host*/*"    
  2037   +    ;    
  2038   +.version-14.2-env = VS160COMNTOOLS ProgramFiles ProgramFiles(x86) ;    
```  
  
I'm not entirely sure what the effect of this is though, it seemed to work without it on a machine that only had 2019 installed.

---

## Comment 11

> Username: swatanabe  
> Created_at: 2019-03-05 01:36:19 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469498896  

AMDG  
  
On 3/4/19 6:31 PM, Tom Kent wrote:  
> Yeah, these two are sloppy copy-paste errors  
> ```  
>  2034   +.version-14.2-path =    
>   2035   +    "../../VC/Tools/MSVC/*/bin/Host*/*"    
>   2036   +    "Microsoft Visual Studio/2017/*/VC/Tools/MSVC/*/bin/Host*/*"    
>   2037   +    ;    
>   2038   +.version-14.2-env = VS150COMNTOOLS ProgramFiles ProgramFiles(x86) ;    
> ```  
>   
> Should be  
> ```  
>  2034   +.version-14.2-path =    
>   2035   +    "../../VC/Tools/MSVC/*/bin/Host*/*"    
>   2036   +    "Microsoft Visual Studio/2019/*/VC/Tools/MSVC/*/bin/Host*/*"    
>   2037   +    ;    
>   2038   +.version-14.2-env = VS160COMNTOOLS ProgramFiles ProgramFiles(x86) ;    
> ```  
>   
> I'm not entirely sure what the effect of this is though, it seemed to work without it on a machine that only had 2019 installed.  
>   
  
The reason that it worked is that these definitions were  
ignored due to vswhere finding VS2019.  
  
In Christ,  
Steven Watanabe

---

## Comment 12

> Username: teeks99  
> Created_at: 2019-03-05 01:36:31 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469498933  

> I missed this one because I explicitly enumerate all my versions of msvc. Maybe we should require vswhere for 14.2 and drop the environmental variable fallback. Also, should it be VS150COMNTOOLS or VS160COMNTOOLS? In Christ, Steven Watanabe  
  
Unlike 14.1 where the first version of VS2017 didn't ship with vswhere, all versions of 2019 ship with it. Unfortunately, to find vswhere we depend on it being installed in the canonical location (e.g. `%PROGRAMFILESx86%\Microsoft Visual Studio\Installer\vswhere.exe` (possibly %PROGRAMFILES% on 32-bit OS?). If the user installs elsewhere I'm not sure how to find it.

---

## Comment 13

> Username: pdimov  
> Created_at: 2019-03-05 01:52:11 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469502315  

> The reason that it worked is that these definitions were ignored due to vswhere finding VS2019.  
  
That's probably why it works for me and detects msvc-14.1, whereas it detects msvc-14.2 on Appveyor. Appveyor probably doesn't have vswhere, so it uses the fallback path and due to the typos both 14.1 and 14.2 match.

---

## Comment 14

> Username: pdimov  
> Created_at: 2019-03-05 01:58:39 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469503717  

> I'm not entirely sure what the effect of this is though  
  
The effect of it, as already stated, is that it autodetects 14.2 when only VS2017 is installed.

---

## Comment 15

> Username: swatanabe  
> Created_at: 2019-03-05 02:00:39 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469504206  

AMDG  
  
On 3/4/19 6:52 PM, Peter Dimov wrote:  
> That's probably why it works for me and detects msvc-14.1, whereas it detects msvc-14.2 on Appveyor. Appveyor probably doesn't have vswhere, so it uses the fallback path and due to the typos both 14.1 and 14.2 match.  
>   
  
Even if it has vswhere, I /think/ the fallback gets  
used if vswhere doesn't find the toolset.  This code  
is giving me a headache.  
  
In Christ,  
Steven Watanabe

---

## Comment 16

> Username: grafikrobot  
> Created_at: 2019-03-05 02:23:54 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469509224  

On Mon, Mar 4, 2019, 7:36 PM Tom Kent <notifications@github.com> wrote:  
  
> Unfortunately, to find vswhere we depend on it being installed in the  
> canonical location (e.g. %PROGRAMFILESx86%\Microsoft Visual  
> Studio\Installer\vswhere.exe (possibly %PROGRAMFILES% on 32-bit OS?). If  
> the user installs elsewhere I'm not sure how to find i  
>  
When vswhere was created we specifically asked for it to be installed in a  
canonical location. I sure hope Microsoft didn't change that.

---

## Comment 17

> Username: swatanabe  
> Created_at: 2019-03-05 02:31:26 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469510852  

AMDG  
  
Alright, I hope that does it: 4ad28074  
  
In Christ,  
Steven Watanabe

---

## Comment 18

> Username: teeks99  
> Created_at: 2019-03-05 02:54:00 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469515577  

> On Mon, Mar 4, 2019, 7:36 PM Tom Kent ***@***.***> wrote: Unfortunately, to find vswhere we depend on it being installed in the canonical location (e.g. %PROGRAMFILESx86%\Microsoft Visual Studio\Installer\vswhere.exe (possibly %PROGRAMFILES% on 32-bit OS?). If the user installs elsewhere I'm not sure how to find i  
> When vswhere was created we specifically asked for it to be installed in a canonical location. I sure hope Microsoft didn't change that.  
  
I meant that if users select a different install path during setup it might not be there. Not sure about that though, the selection might just be for the actual studio instance, the "Install" path might not be easily changeable.

---

## Comment 19

> Username: grafikrobot  
> Created_at: 2019-03-05 03:01:10 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469517068  

On Mon, Mar 4, 2019 at 8:54 PM Tom Kent <notifications@github.com> wrote:  
  
> On Mon, Mar 4, 2019, 7:36 PM Tom Kent ***@***.***> wrote: Unfortunately,  
> to find vswhere we depend on it being installed in the canonical location  
> (e.g. %PROGRAMFILESx86%\Microsoft Visual Studio\Installer\vswhere.exe  
> (possibly %PROGRAMFILES% on 32-bit OS?). If the user installs elsewhere I'm  
> not sure how to find i  
> When vswhere was created we specifically asked for it to be installed in a  
> canonical location. I sure hope Microsoft didn't change that.  
>  
> I meant that if users select a different install path during setup it  
> might not be there. Not sure about that though, the selection might just be  
> for the actual studio instance, the "Install" path might not be easily  
> changeable.  
>  
The "Installer" path is not alterable. Which is why it's in a common  
location. That intentionally the case before. If it's no longer the case  
please let us know so we can go scream at Microsoft for recreating the  
unknown install location mess yet again.  
  
--   
-- Rene Rivera  
-- Grafik - Don't Assume Anything  
-- Robot Dreams - http://robot-dreams.net

---

## Comment 20

> Username: teeks99  
> Created_at: 2019-03-05 13:04:15 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469671993  

> AMDG Alright, I hope that does it: [4ad2807](https://github.com/boostorg/build/commit/4ad28074f43fbde705aee2c71ec64acc56ceea18) In Christ, Steven Watanabe  
  
I've verified that this still works correctly on a machine with (only) 14.2 installed.   
  
*   Cmd.exe bootstrap.bat finds 14.2 via vswhere  
*   Cmd.exe b2 builds with toolset=14.2 via vswhere  
*   VS2019 cmd prompt bootstrap.bat fails when no vswhere found (this was probably happening on the original commit, added #403 to address)  
*   VS2019 cmd prompt b2 builds with toolset=14.2 via VS160COMNTOOLS

---

## Comment 21

> Username: teeks99  
> Created_at: 2019-03-05 22:57:23 UTC  
> Updated_at: 2019-03-05 22:57:48 UTC  
> Url: https://github.com/boostorg/build/pull/401#issuecomment-469893027  

> The "Installer" path is not alterable. Which is why it's in a common location. That intentionally the case before. If it's no longer the case please let us know so we can go scream at Microsoft for recreating the unknown install location mess yet again.  
  
I wasn't aware of that, generally on windows install paths are just a default and thus modifiable. However, after investigating a bit, there doesn't seem to be any way to change the path to the "Microsoft Visual Studio/Installer" path, and that holds true for VS2019.

---
