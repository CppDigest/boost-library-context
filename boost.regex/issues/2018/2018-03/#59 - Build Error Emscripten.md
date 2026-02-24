# #59 - Build Error Emscripten [Closed]

> Username: jspears  
> Created at: 2018-03-19 17:43:35 UTC  
> Updated at: 2022-10-06 04:25:31 UTC  
> Closed at: 2018-03-21 06:02:14 UTC  
> Url: https://github.com/boostorg/regex/issues/59  

When running   
  
```  
./b2 toolset=emscripten --with-regex  
```  
It errors with  
```  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : no  (cached)  
    - combined                 : no  (cached)  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes (cached)  
Error: ambiguity found when searching for best transformation  
Trying to produce type 'SEARCHED_LIB' from:   
Generators that succeeded:  
 -  searched-lib-generator  
 -  emscripten.searched-lib-generator  
First generator produced:   
 -  { %.no-action-icuuc.SEARCHED_LIB }  
Second generator produced:   
 -  { %.no-action-icuuc.SEARCHED_LIB }  
```  
OS: OSX -10.12.6  
Compiler: emcc (Emscripten gcc/clang-like replacement) 1.37.33 ()  
  
Most boost libraries build fine using the ./b2 toolset=emscripten --with-*  though graph  
iostreams,  
locale,  
log,  
python,  
regex,  
stacktrace  
 Fail in the same way.     
  
Perhaps this is a b2 problem.    I'm new to C++ so it is probably me...

---

## Comment 1

> Username: jspears  
> Created at: 2018-03-21 06:02:14 UTC  
> Url: https://github.com/boostorg/regex/issues/59#issuecomment-374840830  

Ah sorry the docs had the --disable-icu and didn't fugure how that mapped to this issue; Closing, thanks.

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2018-07-20 19:41:02 UTC  
> Url: https://github.com/boostorg/regex/issues/59#issuecomment-406706829  

what was the solution to this problem?  i just encountered it when trying to build boost 1.67 with emscripten 1.38.8 on OSX

---

## Comment 3

> Username: jspears  
> Created at: 2018-07-23 17:14:30 UTC  
> Url: https://github.com/boostorg/regex/issues/59#issuecomment-407133165  

apparently it had to do with --disable-icu.   I don't exactly remember.  The answer was in the error message ```%.no-action-icuuc.SEARCHED_LIB ``` .

---

## Comment 4

> Username: ofloveandhate  
> Created at: 2018-07-23 21:56:55 UTC  
> Url: https://github.com/boostorg/regex/issues/59#issuecomment-407214174  

ah ok.  i tried `--disable-icu` and it yielded almost the same error, so something additional is up for me.  i think i'll investigate what you indicate, and possibly raise the issue at boost/build.  thanks!

---

## Comment 5

> Username: SwimmingTiger  
> Created at: 2018-07-24 09:27:05 UTC  
> Updated at: 2018-07-24 10:38:24 UTC  
> Url: https://github.com/boostorg/regex/issues/59#issuecomment-407342166  

```  
boost_1_67_0# ./bootstrap.sh --with-libraries=system,log,date_time,filesystem,thread  
boost_1_67_0# ./b2 toolset=emscripten link=static  
/mnt/c/work/asm.js/boost_1_67_0/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : 32-bit  
    - default architecture     : none  
  
Building the Boost C++ Libraries.  
  
    - symlinks supported       : yes (cached)  
Error: ambiguity found when searching for best transformation  
Trying to produce type 'SEARCHED_LIB' from:  
Generators that succeeded:  
 -  searched-lib-generator  
 -  emscripten.searched-lib-generator  
First generator produced:  
 -  { %.no-action-psapi.SEARCHED_LIB }  
Second generator produced:  
 -  { %.no-action-psapi.SEARCHED_LIB }  
```  
  
`{ %.no-action-psapi.SEARCHED_LIB }`  
How can I do for this?  
  
```  
cat /etc/os-release  
NAME="Ubuntu"  
VERSION="16.04.5 LTS (Xenial Xerus)"  
  
emcc --version  
emcc (Emscripten gcc/clang-like replacement) 1.38.9 (commit 97564e0bbf82fe99c3021176ac3835cdbc2da687)  
```  
  
--------------------------------------------------------------------------  
  
I don't know why it want to `psapi`, it looks like Windows only but i'm in Linux (WSL, should be considered as Linux and cannot detect Windows features).  
  
This also not work:  
```  
./b2 toolset=emscripten link=static threading=multi host-os=linux target-os=linux architecture=x86 address-model=32 cflags="-s USE_PTHREADS=1" cxxflags="-s USE_PTHREADS=1"  
```  
  
--------------------------------------------------------------------------  
  
I found the problem is in `Boost.Log`, so I disabled it and build successed.  
  
```  
 ./bootstrap.sh --with-libraries=system,date_time,filesystem,thread  
./b2 toolset=emscripten link=static threading=multi cflags="-s USE_PTHREADS=1" cxxflags="-s USE_PTHREADS=1"  
  
The Boost C++ Libraries were successfully built!  
```

---

## Comment 6

> Username: volo-zyko  
> Created at: 2021-04-28 19:06:16 UTC  
> Url: https://github.com/boostorg/regex/issues/59#issuecomment-828707747  

BTW, for me the following change fixed the "ambiguity found when searching for best transformation" error:  
  
```diff  
--- tools/build/src/tools/emscripten.jam  
+++ tools/build/src/tools/emscripten.jam  
@@ -6,6 +6,7 @@  
 import feature ;  
 import os ;  
 import toolset ;  
+import generators ;  
 import common ;  
 import gcc ;  
 import type ;  
@@ -52,6 +53,8 @@  
         <debug-symbols>off <debug-symbols>on  
         <rtti>off <rtti>on  
         ;  
+generators.override builtin.lib-generator : emscripten.prebuilt ;  
+generators.override emscripten.searched-lib-generator : searched-lib-generator ;  
   
 type.set-generated-target-suffix EXE : <toolset>emscripten : "js" ;  
 type.set-generated-target-suffix OBJ : <toolset>emscripten : "bc" ;  
   
```

---

## Comment 7

> Username: werto87  
> Created at: 2021-05-23 21:17:38 UTC  
> Updated at: 2021-05-23 21:18:24 UTC  
> Url: https://github.com/boostorg/regex/issues/59#issuecomment-846625724  

SwimmingTiger and volo-zyko solution worked for me.  
Note: I had to change the suffix from ".bc" into ".a" to use the lib with cmake on linux.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2021-05-24 08:03:24 UTC  
> Url: https://github.com/boostorg/regex/issues/59#issuecomment-846856478  

Just to add, that with the latest Boost release, regex is header only for C++11 and up, so building it is unnecessary.

---

## Comment 9

> Username: ghost  
> Created at: 2021-11-09 07:53:47 UTC  
> Updated at: 2022-10-06 04:25:31 UTC  
> Url: https://github.com/boostorg/regex/issues/59#issuecomment-963896279  

I was able to compile boost::python for emscripten using the info from SwimmingTiger and volo-zyko as well.  I used the command : `./b2 toolset=emscripten link=static variant=release threading=single runtime-link=static python`  
One thing to mention was that I was getting the error `# error unknown multiarch location for pyconfig.h` and had to use `pyconfig.h` generated from compiling python for emscripten (I followed this open source project [here](https://github.com/python-emscripten/python), but It would probably work if you have built python with the emscripten toolchain already).   
  
I couldn't figure out how to set the custom python include path to the new pyconfig.h location  
so i just temporarily overrode pyconfig.h at /usr/include/python2.7 with the custom pyconfig.h  
and built and the error for multiarch suport for pyconfig.h was gone, and then converted the libboost_python27.bc to .a and it works a treat when I link to it in my `emcc` command.  
  
Edit: There is an exception being thrown when trying to call into boost::python, i will update with more info when I can.  
Edit2: I couldnt fix the exception, but it was being thrown by `boost::python::exec` and `boost::python::get_override`, I think perhaps there is some missing functions because I was able to replace `exec` with `PyImport_AppendInittab` and `get_override` with `call_method` using  [this](https://wiki.python.org/moin/boost.python/OverridableVirtualFunctions).

---

## Comment 10

> Username: mpconte  
> Created at: 2022-05-24 20:02:04 UTC  
> Url: https://github.com/boostorg/regex/issues/59#issuecomment-1136377766  

On building boost 1.75.0 with the emscripten toolset and following the comments here to build, I still get the following error in the `./b2 `step:  
```  
  
wasm-ld: error: unknown argument: -h  
wasm-ld: error: unknown argument: -Bstatic  
wasm-ld: error: unknown argument: -Bdynamic  
```
