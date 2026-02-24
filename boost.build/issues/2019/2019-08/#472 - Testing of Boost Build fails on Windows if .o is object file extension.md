# #472 - Testing of Boost Build fails on Windows if .o is object file extension [Closed]

> Username: eldiener  
> Created at: 2019-08-26 14:32:54 UTC  
> Updated at: 2019-11-19 18:18:18 UTC  
> Closed at: 2019-11-19 18:18:18 UTC  
> Url: https://github.com/boostorg/build/issues/472  

A number of tests of Boost Build on Windows fail if the toolset chosen to be used for the test creates a .o object file extension instead of a .obj object file extension. A Windows compiler which creates a .o object file extension is mingw-w64/gcc.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-08-27 00:05:18 UTC  
> Url: https://github.com/boostorg/build/issues/472#issuecomment-525080162  

AMDG  
  
On 8/26/19 8:32 AM, Edward Diener wrote:  
> A number of tests of Boost Build on Windows fail if the toolset chosen to be used for the test creates a .o object file extension instead of a .obj object file extension. A Windows compiler which creates a .o object file extension is mingw-w64/gcc.  
>   
  
There is code that is supposed to handle this here:  
https://github.com/boostorg/build/blob/develop/test/BoostBuild.py#L134  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: eldiener  
> Created at: 2019-08-27 16:45:45 UTC  
> Url: https://github.com/boostorg/build/issues/472#issuecomment-525386430  

```  
def get_toolset():  
    toolset = None  
    for arg in sys.argv[1:]:  
        if not arg.startswith("-"):  
            toolset = arg  
    return toolset or "gcc"  
  
```  
  
If I pass 'gcc-9.2', the toolset is not 'gcc' since the code above makes no effort to separate the toolset from the version.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2019-08-27 18:05:28 UTC  
> Url: https://github.com/boostorg/build/issues/472#issuecomment-525417451  

@eldiener right.. don't pass `gcc-9.2`, pass just `gcc`. It's not meant to be a fully robust and comprehensive test system. It's meant to be the minimum to test regular b2 development. So I guess.. Why are you passing `gcc-9.2`?

---

## Comment 4

> Username: eldiener  
> Created at: 2019-08-27 21:56:57 UTC  
> Url: https://github.com/boostorg/build/issues/472#issuecomment-525499115  

I am passing 'gcc-9.2' because I want to use the gcc-9.2 toolset that is in my user-config.jam, else how does the test system know what toolset to use in my user-config.jam ? I have about 40 different toolsets in my user-config.jam on Windows for different versions of mingw-w64/gcc, intel, clang-win, clang-linux, and msvc. Furthermore the actual test_system.html ( fot which I already have a PR ) has as examples:  
  
"Test scripts will use the toolset you configured to be the default or you can specify a specific one on the command line:  
  
python test_all.py borland  
python generators_test.py msvc-7.1"  
  
so it certainly looks like telling me to give the full toolset, as in the second example, is OK. I will submit another PR which fixes the get_toolset() so that it searches for a '-' in the toolset and only returns the compiler portion.

---

## Comment 5

> Username: eldiener  
> Created at: 2019-08-28 04:31:10 UTC  
> Url: https://github.com/boostorg/build/issues/472#issuecomment-525579622  

I added a fix to my original PR at https://github.com/boostorg/build/pull/470.
