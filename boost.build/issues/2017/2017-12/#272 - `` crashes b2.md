# #272 - `[ $(target).sources ]` crashes b2 [Closed]

> Username: pdimov  
> Created at: 2017-12-16 16:34:11 UTC  
> Updated at: 2017-12-18 15:59:07 UTC  
> Closed at: 2017-12-16 21:41:37 UTC  
> Url: https://github.com/boostorg/build/issues/272  

This code:  
  
```  
import project ;  
  
BOOST_ROOT = ../boost ;  
  
local lib = atomic ;  
local path = $(BOOST_ROOT)/libs/$(lib)/build ;  
local project = [ project.load $(path) ] ;  
local pt = [ project.target $(project) ] ;  
local mt = [ $(pt).main-target stage ] ;  
  
if $(mt)  
{  
    local libs = [ $(mt).sources ] ;  
}  
```  
  
results in `b2.exe` crashing.

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-12-16 17:09:35 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352196354  

AMDG  
  
This is a duplicate of #260.  
(main-target has no `sources` member)  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created at: 2017-12-16 17:17:23 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352196888  

Yes, I actually have no idea what I'm doing here, but I figured it shouldn't crash.  
  
What I'm trying to do is obtain the `$(libraries)` part of  
  
`install stage : $(libraries) : <location>... ;`  
  
so that I can then declare  
  
`alias something : $(libraries) ;`

---

## Comment 3

> Username: swatanabe  
> Created at: 2017-12-16 18:17:04 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352200909  

AMDG  
  
On 12/16/2017 10:17 AM, Peter Dimov wrote:  
> Yes, I actually have no idea what I'm doing here, but I figured it shouldn't crash.  
>   
  
Right.  It's a pretty simple fix, so I'll get to it shortly.  
  
> What I'm trying to do is obtain the `$(libraries)` part of  
>   
> `install stage : $(libraries) : <location>... ;`  
>   
> so that I can then declare  
>   
> `alias something : $(libraries) ;`  
>   
  
Don't try to do this at the main target level.  
Instead, try something like this (untested):  
  
# sources is a list of virtual-targets  
rule alias-sources-impl ( project name : property-set : sources * )  
{  
    # DFS of the target graph, which stops when  
    # it reaches another main-target.  
    local target-graph ;  
    for local s in $(sources)  
    {  
        target-graph += [ virtual-target.traverse $(s)  
          : include-sources : include-roots ] ;  
    }  
    # Remove targets created by the main target  
    local result ;  
    for local t in $(target-graph)  
    {  
        if [ $(t).root ] && ! ( $(t) in $(sources) )  
        {  
            result += $(t) ;  
        }  
    }  
    return $(result) ;  
}  
  
generate something : stage : <generating-rule>@alias-sources-impl ;  
  
Notes: virtual-target.traverse is pretty simple, you  
might be better off copying it and adjusting the  
results it returns to be what you want.  I suppose  
you could also take the set difference of the output  
of virtual-target.traverse with and without the  
sources and roots included.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: pdimov  
> Created at: 2017-12-16 19:02:32 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352204266  

Thanks Steven, this works. (I just dropped the `include-sources` argument.) The list returned from `alias-sources-impl` looks correct, f.ex. for `test` it's  
  
```  
library-test:  
     libboost_prg_exec_monitor-vc141-mt-gd-x32-1_66.lib  
     libboost_test_exec_monitor-vc141-mt-gd-x32-1_66.lib  
     libboost_unit_test_framework-vc141-mt-gd-x32-1_66.lib  
```  
  
but the problem now is that building `library-test` still stages the library, and I don't want that - that's what I'm trying to avoid by using this instead of the simple  
  
```alias library-test : $(BOOST-ROOT)/libs/test/build//stage ;```

---

## Comment 5

> Username: swatanabe  
> Created at: 2017-12-16 19:36:19 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352206042  

AMDG  
  
On 12/16/2017 12:02 PM, Peter Dimov wrote:  
> Thanks Steven, this works. (I just dropped the `include-sources` argument.) The list returned from `alias-sources-impl` looks correct, <snip>  
>   
  
You might also consider filtering by target-type:  
  [ type.is-derived [ $(t).type ] LIB ]  
  
> but the problem now is that building `library-test` still stages the library, <snip>  
>   
  
That's surprising.  It seems to work for me.  Are you sure  
that stage isn't being built through some other channel?  
  
```  
C:\Users\Steven\Documents\boost\test>..\boost-git\b2 -n -d1 my-alias  
<snip>  
...updating 6 targets...  
compile-c-c++  
..\boost-git\bin.v2\libs\system\build\msvc-14.0\debug\threadapi-win32\threading-multi\error_code.obj  
msvc.link.dll  
..\boost-git\bin.v2\libs\system\build\msvc-14.0\debug\threadapi-win32\threading-multi\boost_system-vc140-mt-gd-x32-1_66.dll  
msvc.manifest.dll  
..\boost-git\bin.v2\libs\system\build\msvc-14.0\debug\threadapi-win32\threading-multi\boost_system-vc140-mt-gd-x32-1_66.dll  
...updated 6 targets...  
```  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: pdimov  
> Created at: 2017-12-16 20:12:13 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352208145  

OK, please see https://github.com/boostorg/check_build. I'm trying to create a submodule whose tests build the libraries, so that we have in the test matrix an entry that shows us which libraries build. It's intended to go into `tools/check_build`.

---

## Comment 7

> Username: swatanabe  
> Created at: 2017-12-16 20:34:41 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352209721  

AMDG  
  
On 12/16/2017 01:12 PM, Peter Dimov wrote:  
> OK, please see https://github.com/boostorg/check_build. I'm trying to create a submodule whose tests build the libraries, so that we have in the test matrix an entry that shows us which libraries build. It's intended to go into `tools/check_build`.  
>   
  
You dropped the loop that removes the targets generated by  
stage.  (Did you misunderstand what include-sources means?  
It means "targets that have no updating actions", not  
"targets in the arguments")  Note that the files have the  
same names as the original libraries, but a different path,  
so you're debug printing won't reveal the error.  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: pdimov  
> Created at: 2017-12-16 21:41:36 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352213770  

Right you are. Thanks again for your help.

---

## Comment 9

> Username: pdimov  
> Created at: 2017-12-18 13:37:05 UTC  
> Updated at: 2017-12-18 13:37:18 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352427256  

This works now: http://www.boost.org/development/tests/develop/developer/check_build.html  
  
One remaining wrinkle is that when the library target can't be constructed, the test passes, instead of being dropped.  
  
```  
C:\Projects\boost-git\boost\tools\check_build>b2 test//stacktrace  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
error: No best alternative for ../../libs/stacktrace/build/stage  
    next alternative: required properties: (empty)  
        matched  
    next alternative: required properties: (empty)  
        matched  
warning: Unable to construct test/library-stacktrace  
...found 27 targets...  
...updating 11 targets...  
compile-c-c++ ..\..\bin.v2\tools\check_build\test\stacktrace.test\msvc-8.0\debug  
\link-static\threadapi-win32\threading-multi\main.obj  
main.cpp  
msvc.link ..\..\bin.v2\tools\check_build\test\stacktrace.test\msvc-8.0\debug\lin  
k-static\threadapi-win32\threading-multi\stacktrace.exe  
msvc.manifest ..\..\bin.v2\tools\check_build\test\stacktrace.test\msvc-8.0\debug  
\link-static\threadapi-win32\threading-multi\stacktrace.exe  
testing.capture-output ..\..\bin.v2\tools\check_build\test\stacktrace.test\msvc-  
8.0\debug\link-static\threadapi-win32\threading-multi\stacktrace.run  
        1 file(s) copied.  
**passed** ..\..\bin.v2\tools\check_build\test\stacktrace.test\msvc-8.0\debug\li  
nk-static\threadapi-win32\threading-multi\stacktrace.test  
...updated 11 targets...  
```  
  
Any idea how I can make the test be dropped instead of pass? It's currently  
```  
run main.cpp library-$(lib) : : : : $(lib) ;  
```

---

## Comment 10

> Username: swatanabe  
> Created at: 2017-12-18 15:40:13 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352463913  

AMDG  
  
On 12/18/2017 06:37 AM, Peter Dimov wrote:  
> This works now: http://www.boost.org/development/tests/develop/developer/check_build.html  
>   
> One remaining wrinkle is that when the library target can't be constructed, the test passes, instead of being dropped.  
> <snip>> ```  
> C:\Projects\boost-git\boost\tools\check_build>b2 test//stacktrace  
> <snip>  
> error: No best alternative for ../../libs/stacktrace/build/stage  
>     next alternative: required properties: (empty)  
>         matched  
>     next alternative: required properties: (empty)  
>         matched  
> warning: Unable to construct test/library-stacktrace  
> <snip>  
> ```  
>   
> Any idea how I can make the test be dropped instead of pass? It's currently  
> ```  
> run main.cpp library-$(lib) : : : : $(lib) ;  
> ``  
>   
  
  I think it would be better for the test to fail in  
this case.  This happens because Boost.Stacktrace  
is misusing boost-install.  The easiest way is to  
(ab)use ac.check-library in the requirements:  
  
[ ac.check-library library-$(lib) :  
  <library>library-$(lib) : <build>no ]  
  
Note: ac.check-library is intended to be used for  
ac-library targets, but the implementation actually  
works for any target.  
  
Note 2: To make it fail, use <source>some-file-that-fails-to-compile.cpp  
instead of <build>no.  
  
  Also, I've been thinking about this a bit more, and  
I think that instead of trying to get the libraries  
out of stage, it would be better to get them in the  
same way that Jamroot does (or perhaps make Jamroot  
export a list of libraries).  Just creating an  
alias to libs/$(library)/build should work, although  
it doesn't distinguish between multiple libraries  
from the same project like serialization and wserialization.  
(Using a project as a target is equivalent to  
naming all the non-explicit targets in the project,  
which should be exactly the libraries.)  
  
In Christ,  
Steven Watanabe

---

## Comment 11

> Username: pdimov  
> Created at: 2017-12-18 15:59:07 UTC  
> Url: https://github.com/boostorg/build/issues/272#issuecomment-352469818  

> I think it would be better for the test to fail in this case.  This happens because Boost.Stacktrace is misusing boost-install.  
  
Right, https://github.com/boostorg/stacktrace/pull/39  
  
I should have used `fiber` as the example, as it demonstrates a correct Jamfile that still fails to construct (because the compiler is not supported.) In this case a matrix failure is undesirable.  
  
> [ ac.check-library library-$(lib) : <library>library-$(lib) : <build>no ]  
  
Thanks, I'll try it.  
  
> (Using a project as a target is equivalent to naming all the non-explicit targets in the project, which should be exactly the libraries.)  
  
This was my initial attempt (and remains the fallback when there's no `stage` target), but practice showed that the build Jamfiles contain plenty of non-explicit targets that break the build. :-)  
  
Instead of fiddling with `stage` I was considering to patch `boost-install` to give me  
  
`alias libraries-for-stage : $(libraries) ;`  
  
and just use that, but I decided to go with the existing `stage` after you showed me how to make that work.
