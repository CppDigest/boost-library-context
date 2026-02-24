# #89 - Building with support for lCU breaks other Boost libs (e.g. Boost.FileSystem) [Closed]

> Username: DenizThatMenace  
> Created at: 2019-10-08 08:35:54 UTC  
> Updated at: 2019-10-26 13:39:53 UTC  
> Closed at: 2019-10-26 09:52:33 UTC  
> Url: https://github.com/boostorg/regex/issues/89  

When building the latest Boost releases (starting with **Boost 1.70.0**) and enabling support for **ICU** *Boost.Build* first warns about an incompatible setting and later fails while compiling *Boost.FileSystem* because of different settings regarding support for *ICU*:  
  
```  
 Performing configuration checks  
   
     - default address-model    : 64-bit  
     - default architecture     : x86  
     - C++11 mutex              : yes  
     ...  
     - has_icu builds           : yes  
 warning: non-free usage requirements <runtime-link>shared ignored  
 warning: in main-target icu_options at libs/regex/build/Jamfile.v2:97  
     ...  
     - iconv (libc)             : yes  
     - icu                      : yes  
     - native-atomic-int32-supported : yes  
     ...  
     ...  
 error: Name clash for '<p/home/jenkins/workspace/Pipeline_Boost-1.71.0/gcc8/installed/opt/some/path/lib/x86_64-linux-gnu>libboost_filesystem-gcc8-mt-x64-1_71.so.1.71.0'  
 error:   
 error: Tried to build the target twice, with property sets having   
 error: these incompatible properties:  
 error:   
 error:     -  none  
 error:     -  <dll-path>/opt/some/path/bin <linkflags>-L/opt/some/path/lib/x86_64-linux-gnu <linkflags>-licudata <linkflags>-licui18n <linkflags>-licuuc  
 error:   
 error: Please make sure to have consistent requirements for these   
 error: properties everywhere in your project, especially for install  
 error: targets.  
```  
  
The warning points to line 97 in `libs/regex/build/Jamfile.v2`which seems problematic.    
And indeed, if I patch that file to never enable support for *ICU* in *Boost.Regex* building all of Boost succeeds.  
  
Of course, this is not a solution, because I wanted *ICU* support compiled in. But it seems to have proven that the Jamfile of *Boost.Regex* somehow seems to be broken or at least incompatible.  
  
---  
  
For the sake of completeness, I used the following command when trying to build *Boost*:  
```  
b2 -j4 -q \  
  --build-dir=/home/jenkins/workspace/Pipeline_Boost-1.71.0/gcc8/build/boost \  
  --build-type=minimal \  
  --layout=versioned \  
  address-model=64 \  
  install \  
  --prefix=/home/jenkins/workspace/Pipeline_Boost-1.71.0/gcc8/installed/opt/some/path \  
  --libdir=/home/jenkins/workspace/Pipeline_Boost-1.71.0/gcc8/installed/opt/some/path/lib/x86_64-linux-gnu \  
  --cmakedir=/home/jenkins/workspace/Pipeline_Boost-1.71.0/gcc8/installed/opt/some/path/share/boost \  
  dll-path=/opt/some/path/lib/x86_64-linux-gnu \  
  -sICU_PATH="/opt/some/path" \  
  -sICU_LINK="-L/opt/some/path/lib/x86_64-linux-gnu -licuuc -licudata -licui18n" \  
  toolset=gcc-8 \  
  cflags="-m64 -fpic -fdiagnostics-color=always -O3 -g" \  
  cxxflags="-m64 -fpic -fdiagnostics-color=always -O3 -g -std=c++17" \  
  linkflags="-Wl,--as-needed"  
```  
  
The *ICU* libs that should be used are in the denoted location under `/opt/some/path/lib/x86_64-linux-gnu`.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-10-08 12:45:09 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-539495366  

I can reproduce this, reduced command line is:  
  
```  
./b2  -sICU_PATH="/opt/some/path"   -sICU_LINK="-L/opt/some/path/lib/x86_64-linux-gnu -licuuc -licudata -licui18n"   toolset=gcc-8 -n  
```  
  
However, I suspect Boost.Regex is a bystander here, if I add `--with-regex --with-filesystem` to the command line then everything builds fine, the culprit appears to be Boost.Log, because the minimal reproducer is:  
  
```  
./b2  -sICU_PATH="/opt/some/path"   -sICU_LINK="-L/opt/some/path/lib/x86_64-linux-gnu -licuuc -licudata -licui18n"   toolset=gcc-8 -n --with-filesystem  --with-log  
```  
In any case we will need a Boost.Build expert to solve this one!

---

## Comment 2

> Username: Lastique  
> Created at: 2019-10-08 13:54:48 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-539525176  

I'm not an expert in Boost.Build but it seems that Boost.Log triggers the problem because it depends (by default) both on Boost.Regex and a few other Boost libraries, like Filesystem, DateTime, Thread, etc. It is those other dependent libraries are randomly mentioned in the error message, sometimes different from one run to another, depending on which ones are specified in the command line.  
  
It seems like Boost.Build doesn't propagate `<linkflags>` to some of Boost.Log dependent libraries, which are left to build both with the custom `<linkflags>` and without. I don't see what I can do about it in Boost.Log. Looks like a Boost.Build issue to me.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-10-15 01:03:34 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-541990969  

Since it looks like Boost.Regex will add `-licuuc -licudata -licui18n` by itself if `ICU_LINK` is not specified, you can try omitting it, or if `-L/opt/some/path/lib/x86_64-linux-gnu` is required, try adding it via `linkflags=-L/opt/some/path/lib/x86_64-linux-gnu` instead of `ICU_LINK`.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-10-15 01:07:28 UTC  
> Updated at: 2019-10-15 01:08:09 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-541991712  

> It seems like Boost.Build doesn't propagate `<linkflags>` to some of Boost.Log dependent libraries  
  
The problem is the opposite; that it does propagate it. Log inherits the `<linkflags>` from Regex and passes it down to Filesystem, but the other copy of Filesystem (requested via `--with-filesystem`) doesn't have the `<linkflags>` because it doesn't depend on Regex from which to get them.

---

## Comment 5

> Username: Lastique  
> Created at: 2019-10-15 06:22:34 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-542057113  

I'm assuming every target should have the same linkflags, regardless whether it uses ICU or not. Indeed, some linker flags need to be applied to every linking target.  
  
From Steven's response on the ML it sounds like `--with-filesystem` creates a new root and a separate dependency tree, which does not have linkflags and hence we have a problem. My understanding was that there is only one root and only one dependency tree during the build, and `--with-*` flags only whitelist some parts of the tree. I still think this would probably make more sense and would solve the problem, but I understand this might be a difficult change.  
  
Regarding using linkflags instead of variables, the latter are [documented](https://www.boost.org/doc/libs/1_71_0/libs/regex/doc/html/boost_regex/install.html) as the interface for customizing ICU location. The docs will need to be updated.

---

## Comment 6

> Username: DenizThatMenace  
> Created at: 2019-10-15 08:42:05 UTC  
> Updated at: 2019-10-15 08:42:25 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-542105628  

I can confirm that building boost succeeds when omitting the `ICU_LINK` option and instead providing the linker-search-path via `linkflags=-L/opt/some/path/lib/x86_64-linux-gnu`, as @pdimov described above.  
  
If this is the desired solution, the documentation should indeed be changed as @Lastique already suggested.

---

## Comment 7

> Username: pdimov  
> Created at: 2019-10-15 09:31:40 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-542125380  

Creating a single root has at least two disadvantages I can think of; first, each library's usage requirements will propagate to every other, regardless of whether that other is a dependency. That is, at the moment Boost.Regex affects Filesystem through Log, with a single root it will affect all. Second, `b2 --with-filesystem` and `b2 --with-filesystem --with-log` will still use different options for Filesystem.  
  
It seems to me that Regex shouldn't be setting `<linkflags>` in a usage requirement. Instead, there should be a mechanism to add that `-L` to `<search>` of the appropriate ICU `lib` targets. Although I can guess why things are done this way - the library names may well need to be customized on some oddball configuration.

---

## Comment 8

> Username: Lastique  
> Created at: 2019-10-15 10:00:13 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-542136760  

For static libraries one has to propagate `-l` and `-L` to the dependent targets. Though it only needs to propagate upwards but not downwards (i.e. from Regex to Log, but not from Log to Filesystem).

---

## Comment 9

> Username: pdimov  
> Created at: 2019-10-15 11:49:23 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-542173247  

This should happen automatically when using `lib`, as is the case here when `ICU_LINK` isn't set.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2019-10-15 17:33:14 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-542322848  

Clearly I will fix this in whatever way will work.  
  
If all you need is to set custom include and library search paths then using `include=` and `linkflags=` on the command line will suffice (I hope - do we need dll-path as well?).  The main use case for ICU_LINK is to set the linker command line in cases where the names of the ICU libraries are non-standard.  ICU has form here in that the names have changed at least once, are already different on windows and linux (anywhere else?) and between debug and static link versions.  
  
Suggested changes and improvements welcome.  Otherwise I don't really know what to do to fix this.

---

## Comment 11

> Username: pdimov  
> Created at: 2019-10-15 17:55:47 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-542331842  

I don't really know how this should be handled in an idiomatic Boost.Build manner; maybe @swatanabe knows and hasn't switched off his notifications.  
  
What I would do here is try to use the built-in `lib` as in the branch when `ICU_LINK` isn't set, but provide name override variables for each ICU library. Something like `ICU_NAME_ICUUC`, `ICU_NAME_ICUDT`, `ICU_NAME_ICUIN` (or perhaps `ICU_ICUUC_NAME`? or just `ICUUC_NAME`? not sure.)  
  
For the libpath, either a common `ICU_LIBPATH` or per-library ones, then add those in `<search>` instead of, or in addition to, the current default.  
  
Not sure about the dll-path.

---

## Comment 12

> Username: pdimov  
> Created at: 2019-10-15 18:01:18 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-542334032  

I suppose the "right" way to do it is to add `icu.jam` to the Boost.Build tools, along the lines of the existing https://github.com/boostorg/build/blob/develop/src/tools/zstd.jam or https://github.com/boostorg/build/blob/develop/src/tools/openssl.jam.

---

## Comment 13

> Username: swatanabe  
> Created at: 2019-10-16 01:05:25 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-542466106  

AMDG  
  
On 10/15/19 12:01 PM, Peter Dimov wrote:  
> I suppose the "right" way to do it is to add `icu.jam` to the Boost.Build tools, along the lines of the existing https://github.com/boostorg/build/blob/develop/src/tools/zstd.jam or https://github.com/boostorg/build/blob/develop/src/tools/openssl.jam.  
>   
  
A bit more digging, shows that the problem is *not*  
copying the file.  Otherwise there are lots of other  
properties that would cause problems.  The actual cause  
is that stage relinks executables and libraries when  
their dll-path properties change.  That's why only  
the options that affect the linker show up in the error.  
  
I don't think that making `icu.jam` will actually  
help with this problem.  If you need to set rpath  
on boost_regex.so, there's currently no good way to  
achieve it without using dll-path in the usage-requirements  
which is what triggers this error in the first place.  
  
The problem could be avoided if boost-install.jam didn't  
use <install-dependencies>on and instead only directly  
added libraries that have no corresponding stage target,  
but that'll be quite a pain to implement.  
  
In Christ,  
Steven Watanabe

---

## Comment 14

> Username: pdimov  
> Created at: 2019-10-16 14:09:09 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-542719839  

Thanks Steven.  
  
So the problem is that we need to pass `<dll-path>` to the `install` rule, and the only way to do it via usage requirements, which breaks.  
  
This problem is actually not introduced by the commit adding `<install-dependencies>on` to the `stage` target, because the `install` target already had it, as `package.install` adds `<install-dependencies>on`. (https://github.com/boostorg/boost_install/blob/develop/boost-install.jam#L983 https://github.com/boostorg/build/blob/develop/src/tools/package.jam#L237)  
  
Am I right in thinking that the correct fix here is to add a "requirements" parameter to `boost-install`, which should then be passed to `package.install` and `install`? This will allow Regex to do  
  
```  
boost-install boost_regex : <dll-path>$(ICU_DLLPATH) ;  
```  
  
instead of putting the `dll-path` in a usage requirement.

---

## Comment 15

> Username: pdimov  
> Created at: 2019-10-23 00:04:21 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-545206235  

Update: what I suggested above doesn't work. When I implement it, the result is  
  
```  
error: Name clash for '<p/home/pdimov/.local/lib>libboost_regex.so.1.72.0'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  none  
error:     -  <dll-path>/opt/testpath/bin  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
That's presumably because libboost_regex.so installed by Regex has the `<dll-path>` and the one indirectly installed by Log via `<install-dependencies>on` does not. :-/  
  
I don't have any better ideas at this point.  
  
For reference, the changes I tried are https://github.com/boostorg/boost_install/commit/3ebce827724f481269af9769adf393e1d36c52ca, https://github.com/boostorg/boost/commit/f5fecfc1dca4a3f517804e7d270017732e5b8738, https://github.com/boostorg/regex/commit/1dc5c2641388ca5eafee4b313b059371e0f68b4d.

---

## Comment 16

> Username: jzmaddock  
> Created at: 2019-10-25 19:07:15 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-546475777  

I've confirmed I can fix this by removing the dll-path property when ICI_LINK is set.  
  
Or I can fix by using macros to define possible custom names for the ICU libraries.  
  
The latter looks cleaner to me, but in the process of documenting this, I ran into another issue:  
  
What is the correct way to tell b2 where to look for external libraries?  
  
If I use  
  
```  
b2 search=/opt/some/path  
```  
  
Then almost nothing gets built (only log and locale as far as I can see), should this not work?  And should "search" be a documented feature?  
  
Alternatively  
  
```  
b2 linkflags=-L/opt/some/path  
```  
  
Add the options to the end of the command line (after all the object files) which I assume means that the library path will not be used when resolving symbols from those files?  Or am I misunderstanding the -L option?  I confess I can never remember which options are order specific :(

---

## Comment 17

> Username: pdimov  
> Created at: 2019-10-25 19:11:39 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-546477235  

I think that the right feature for setting the global library path is `<library-path>`. `<search>` is only meaningful as a requirement for `lib`.

---

## Comment 18

> Username: pdimov  
> Created at: 2019-10-26 13:39:53 UTC  
> Url: https://github.com/boostorg/regex/issues/89#issuecomment-546603475  

Either `<conditional>@path_options` or `<search>$(ICU_PATH)/bin <search>$(ICU_PATH)/lib` (or perhaps just use `<search>$(ICU_PATH)/bin <search>$(ICU_PATH)/lib <search>$(ICU_PATH)/bin64 <search>$(ICU_PATH)/lib64` everywhere) probably still needs to be given to `lib` even when the names are overridden.
