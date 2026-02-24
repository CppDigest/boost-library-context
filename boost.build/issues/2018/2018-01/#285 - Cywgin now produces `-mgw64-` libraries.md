# #285 - Cywgin now produces `-mgw64-` libraries? [Closed]

> Username: pdimov  
> Created at: 2018-01-17 21:36:22 UTC  
> Updated at: 2018-01-27 00:06:44 UTC  
> Closed at: 2018-01-26 23:12:36 UTC  
> Url: https://github.com/boostorg/build/issues/285  

For some reason, Cygwin g++ now produces  
  
```  
gcc.archive ..\..\bin.v2\libs\system\build\gcc-6.4.0\debug\link-static\libboost_system-mgw64-d-x32-1_67.a  
```  
  
whereas if I remember correctly it used to produce `-gcc64-`. No idea what caused that; I see no relevant changes to `gcc.jam` that would have caused it.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-17 22:02:30 UTC  
> Url: https://github.com/boostorg/build/issues/285#issuecomment-358463053  

AMDG  
  
On 01/17/2018 02:36 PM, Peter Dimov wrote:  
> For some reason, Cygwin g++ now produces  
>   
> ```  
> gcc.archive ..\..\bin.v2\libs\system\build\gcc-6.4.0\debug\link-static\libboost_system-mgw64-d-x32-1_67.a  
> ```  
>   
> whereas if I remember correctly it used to produce `-gcc64-`. No idea what caused that; I see no relevant changes to `gcc.jam` that would have caused it.  
>   
  
It's this:  
https://github.com/boostorg/build/commit/0c2f6ba03fe1372789aab027bba2ac8b5df09a0a  
  
If you set target-os=cygwin, it should give -gcc64-  
again.  
  
I think the correct solution is:  
- Automatically set the default target-os, and  
- pass -mno-cygwin for <target-os>windows  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created at: 2018-01-17 23:09:36 UTC  
> Url: https://github.com/boostorg/build/issues/285#issuecomment-358480587  

You mean automatically set `<target-os>cygwin` under Cygwin g++? I wonder how much would this break.

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-01-17 23:17:27 UTC  
> Url: https://github.com/boostorg/build/issues/285#issuecomment-358482267  

AMDG  
  
On 01/17/2018 04:09 PM, Peter Dimov wrote:  
> You mean automatically set `<target-os>cygwin` under Cygwin g++? I wonder how much would this break.  
>   
  
I would hope that it doesn't break anything, since it's  
already what you get if you use a cygwin build of b2.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: pdimov  
> Created at: 2018-01-17 23:29:04 UTC  
> Url: https://github.com/boostorg/build/issues/285#issuecomment-358484731  

From a cursory build, things seem to work with `target-os=cygwin`, and what you outline does sound the correct approach. I suspect there will be `<target-os>windows:` occurrences in Jamfiles that may need readjustment though.

---

## Comment 5

> Username: swatanabe  
> Created at: 2018-01-20 17:49:01 UTC  
> Url: https://github.com/boostorg/build/issues/285#issuecomment-359189109  

AMDG  
  
On 01/17/2018 04:29 PM, Peter Dimov wrote:  
>>From a cursory build, things seem to work with `target-os=cygwin`, and what you outline does sound the correct approach. I suspect there will be `<target-os>windows:` occurrences in Jamfiles that may need readjustment though.  
>   
  
Ugh.  Now I remember why I haven't already done this.  
There's no easy way to set a dependent default for  
a feature, so I'll have to design a way and make sure  
that it works correctly no matter what crazy  
conditionals/composites/default-builds/subfeatures/etc.  
are around.  
  
As a first idea:  
# Based on toolset.add-requirements.  
# Conditionals passed to toolset.add-defaults will  
# be evaluated in the context of the common properties.  
# They will have higher priority than the default value of the  
# feature, but lower priority than the target requirements  
# and build-request.  
# Note: the implementation has the usual problem that we  
# require conditionals to be evaluated in the context of  
# the common properties, but the common properties are  
# the result of evaluating conditionals and merging the  
# build-request, requirements, and defaults.  Therefore  
# evaluation must be a part of the main loop in  
# targets.evaluate-requirements  
toolset.add-defaults <toolset>gcc-7.x.x:<target-os>cygwin ;  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: pdimov  
> Created at: 2018-01-26 23:31:50 UTC  
> Url: https://github.com/boostorg/build/issues/285#issuecomment-360934027  

Works, although the double checks:  
  
```  
C:\boost-git\develop\libs\system>b2 -j8 test toolset=gcc  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - default address-model    : 32-bit  
    - default architecture     : x86 (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
...patience...  
```  
  
may be a symptom of... something.  
  
There's the question of what to do with clang under Cygwin. At present it's as g++ was before, produces `-clang50-` libraries, doesn't use `target-os=cygwin`. This works fine, but seems inconsistent with g++.

---

## Comment 7

> Username: swatanabe  
> Created at: 2018-01-27 00:06:43 UTC  
> Url: https://github.com/boostorg/build/issues/285#issuecomment-360939282  

AMDG  
  
On 01/26/2018 04:31 PM, Peter Dimov wrote:  
> Works, although the double checks:  
>   
> ```  
> C:\boost-git\develop\libs\system>b2 -j8 test toolset=gcc  
> Performing configuration checks  
>   
>     - default address-model    : 32-bit (cached)  
>     - default architecture     : x86 (cached)  
>     - default address-model    : 32-bit  
>     - default architecture     : x86 (cached)  
>     - symlinks supported       : no  (cached)  
>     - junctions supported      : yes (cached)  
>     - hardlinks supported      : yes (cached)  
> ...patience...  
> ```  
>   
> may be a symptom of... something.  
>   
  
  It's a symptom of the fact that Boost.Build has  
no idea what order to evaluate all the conditionals  
in, so it sometimes has to go back and try again.  
The algorithm will eventually converge to the correct  
result, even though some of the intermediate results  
are nonsensical.  
  
> There's the question of what to do with clang under Cygwin. At present it's as g++ was before, produces `-clang50-` libraries, doesn't use `target-os=cygwin`. This works fine, but seems inconsistent with g++.  
>   
  
  clang's autodetection of everything is very basic  
compared to gcc.  Also, clang's toolset tags are  
inconsistent with everything else (using the full  
name instead of a cryptic abbreviation).  
  
In Christ,  
Steven Watanabe
