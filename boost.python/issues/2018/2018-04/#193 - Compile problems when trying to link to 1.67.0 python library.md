# #193 - Compile problems when trying to link to 1.67.0 python library [Closed]

> Username: Silarn  
> Created at: 2018-04-12 22:22:34 UTC  
> Updated at: 2018-07-02 02:04:13 UTC  
> Closed at: 2018-04-13 04:27:44 UTC  
> Url: https://github.com/boostorg/python/issues/193  

I decided to give the RC2 a spin and ran into just one issue in my project. We have a DLL which links with the python .lib in windows, and when the MSVC 2017 compiler attempted to parse the library name headers in detail/config.hpp at line 108:  
  
```cpp  
#define BOOST_LIB_NAME boost_python##PY_MAJOR_VERSION##PY_MINOR_VERSION  
```  
  
...it resulted in a linker error looking for boost_pythonPY_MAJOR_VERSIONPY_MINOR_VERSION-vc141-mt-x64-1_67.lib.  
  
I had to modify the header to something like this in order to link properly:  
```cpp  
#define _BPYCONCAT(A, B) A ## B  
#define BPYCONCAT(A, B) _BPYCONCAT(A, B)  
#define BOOST_LIB_NAME BPYCONCAT(boost_python, BPYCONCAT(PY_MAJOR_VERSION, PY_MINOR_VERSION))  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-04-13 04:05:24 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-381015699  

Can you please try out this fix: https://github.com/boostorg/python/commit/24313709a78c694af4ae29116cd06ea86585aa95 ?  
With a bit of luck we can still get this into the release, but unfortunately I'm unable to test it myself.  
Thanks !

---

## Comment 2

> Username: Silarn  
> Created at: 2018-04-13 04:25:48 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-381019136  

It looks good. I built the project with this change without issue and just to be sure I rebuilt boost. Nothing went wrong.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-04-13 04:27:44 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-381019445  

Thanks for the confirmation. I have asked the Boost release manager whether he could cherry-pick this fix into the release.  
Since this is fixed on the `develop` branch, I'm closing this issue.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2018-04-19 01:32:02 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-382580568  

For the record: this has been merged to `master`. Still waiting for a bugfix release.

---

## Comment 5

> Username: liuaifu  
> Created at: 2018-04-20 01:08:00 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-382930905  

Thank you, this fix is valid.

---

## Comment 6

> Username: anton-matosov  
> Created at: 2018-04-24 18:24:49 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-384032693  

Hi @stefanseefeld, I am experiencing same issue while building a python extension and looking for a way to disable autolinking. Can you suggest a way to do that?

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2018-04-24 18:39:23 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-384037168  

@anton-matosov Apparently the way to achieve this is to define `BOOST_ALL_NO_LIB`, which will disable the autolink logic. For the rest, I suppose that depends on what build system you are using...

---

## Comment 8

> Username: anton-matosov  
> Created at: 2018-04-24 22:30:30 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-384100454  

Appreciate your quick response! It worked. I am using setuptools for the build, so simple '-DBOOST_ALL_NO_LIB' compiler flag got the job done.

---

## Comment 9

> Username: JLVarjo  
> Created at: 2018-05-28 09:55:31 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-392480123  

Please note that this issue seems to be in numpy module also, namely numpy/config.hpp on line 65. By applying this fix also to this file resolved the issue.

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2018-06-04 14:24:39 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-394373292  

Right, thanks for the reminder. Fixed with https://github.com/boostorg/python/commit/b4230e98f665da643030e596c935a4e1bc14dba4.

---

## Comment 11

> Username: illera88  
> Created at: 2018-06-29 22:54:13 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-401493286  

Any idea when Boost is gonna include this fix or if there are nightly builds to get boost with this fix?  
  
thanks

---

## Comment 12

> Username: stefanseefeld  
> Created at: 2018-06-30 14:14:14 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-401543833  

I don't understand the question: this *is* fixed in master, so the upcoming release should work as intended.

---

## Comment 13

> Username: illera88  
> Created at: 2018-07-02 00:39:50 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-401644753  

I'm compiling python boost doing:  
```  
C:\boost_1_67_0>boostrap.exe  
C:\boost_1_67_0>.\b2.exe address-model=64 --with-python runtime-link=static toolset=msvc variant=release link=static --layout=system  
```  
  
It builds fine and I add the generated `libboost_python36.lib` and the include path to my project. The problem is that as this issue states I'm getting:  
  
`LINK : fatal error LNK1104: cannot open file 'boost_pythonPY_MAJOR_VERSIONPY_MINOR_VERSION-vc120-mt-gd-x64-1_67.lib'`  
  
This happens because `boost_1_67_0` still does not have https://github.com/boostorg/python/commit/b4230e98f665da643030e596c935a4e1bc14dba4 fix.  
  
I guess my question is, can boost python being compiled standalone or does it require boost too? If so, how can I build python boost from this repo instead of building it using the latest boost release (which still does not include mentioned fix).   
  
Thank you

---

## Comment 14

> Username: stefanseefeld  
> Created at: 2018-07-02 01:00:13 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-401646210  

@illera88 as I mentioned, the fix is in *master*, and will be included in the 1.68 release. There is no way to fix 1.67, as that was released a while ago, and no bug fix release is planned for that.  
  
If you want to help make sure the upcoming 1.68 release works fine, please download boost master and build that.  
  
The question of whether Boost.Python can be built stand-alone is entirely separate from this issue. The answer is: yes, depending on what build tool you are using, you should be able to grab the current master from Boost.Python and build it with an older Boost version pre-installed on your system. See for example the README.md for how to build using Faber...

---

## Comment 15

> Username: illera88  
> Created at: 2018-07-02 01:31:40 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-401648979  

Thank you @stefanseefeld . Since it was an easy fix I just changed the necessary lines and I made it compile with a 1.67 build.   
  
Just one more question: How can I chose the Python version (and architecture) I want to build python boost against? By default is using Python 3.6 but I have a Python 2.7 I would like to use it against. How can I set that?  
  
Thanks again

---

## Comment 16

> Username: stefanseefeld  
> Created at: 2018-07-02 02:04:13 UTC  
> Url: https://github.com/boostorg/python/issues/193#issuecomment-401652393  

All that depends on what build tools you are using ? Boost.Build or Faber, or something else ?  
In any case, this seems entirely unrelated to this issue. Please open a new one to discuss general build questions.
