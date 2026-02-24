# #705 - Could not find a suitable toolset for clang without gcc installed [Closed]

> Username: mloskot  
> Created at: 2021-01-23 00:09:53 UTC  
> Updated at: 2022-10-21 11:26:09 UTC  
> Closed at: 2021-01-23 16:57:00 UTC  
> Url: https://github.com/boostorg/build/issues/705  

On Ubuntu 20.04 (and reproduced similar on 16.04):  
  
```  
$ rm ~/user-config.jam  
```  
  
```  
$ sudo apt remove gcc g++  
```  
  
```  
$ clang++ --version  
clang version 10.0.0-4ubuntu1  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
Now  
  
```  
$ ./bootstrap.sh  
  
Could not find a suitable toolset.  
  
You can specify the toolset as the argument, i.e.:  
    ./build.sh gcc  
  
Toolsets supported by this script are:  
    acc, clang, como, gcc, intel-darwin, intel-linux, kcc, kylix, mipspro,  
    pathscale, pgi, qcc, sun, sunpro, tru64cxx, vacpp  
  
For any toolset you can override the path to the compiler with the CXX  
environment variable. You can also use additional flags for the compiler  
with the CXXFLAGS environment variable.  
  
A special toolset; cxx, is available which is used as a fallback when a more  
specific toolset is not found and the cxx command is detected. The 'cxx'  
toolset will use the CXX, CXXFLAGS, and LIBS environment variables, if present.  
  
Similarly, the cross-cxx toolset is available for cross-compiling by using the  
BUILD_CXX, BUILD_CXXFLAGS, and BUILD_LDFLAGS environment variables to compile  
binaries that will be executed on the build system. This allows CXX etc. to be  
set for cross-compilers to be propagated to subprocesses.  
  
Building B2 engine..  
  
Could not find a suitable toolset.  
  
You can specify the toolset as the argument, i.e.:  
    ./build.sh gcc  
  
Toolsets supported by this script are:  
    acc, clang, como, gcc, intel-darwin, intel-linux, kcc, kylix, mipspro,  
    pathscale, pgi, qcc, sun, sunpro, tru64cxx, vacpp  
  
For any toolset you can override the path to the compiler with the CXX  
environment variable. You can also use additional flags for the compiler  
with the CXXFLAGS environment variable.  
  
A special toolset; cxx, is available which is used as a fallback when a more  
specific toolset is not found and the cxx command is detected. The 'cxx'  
toolset will use the CXX, CXXFLAGS, and LIBS environment variables, if present.  
  
Similarly, the cross-cxx toolset is available for cross-compiling by using the  
BUILD_CXX, BUILD_CXXFLAGS, and BUILD_LDFLAGS environment variables to compile  
binaries that will be executed on the build system. This allows CXX etc. to be  
set for cross-compilers to be propagated to subprocesses.  
  
  
Failed to build B2 build engine  
```  
  
or   
  
```  
$ ./bootstrap.sh --with-toolset=clang  
Building B2 engine..  
  
Could not find a suitable toolset.  
  
You can specify the toolset as the argument, i.e.:  
    ./build.sh gcc  
  
Toolsets supported by this script are:  
    acc, clang, como, gcc, intel-darwin, intel-linux, kcc, kylix, mipspro,  
    pathscale, pgi, qcc, sun, sunpro, tru64cxx, vacpp  
  
For any toolset you can override the path to the compiler with the CXX  
environment variable. You can also use additional flags for the compiler  
with the CXXFLAGS environment variable.  
A special toolset; cxx, is available which is used as a fallback when a more  
specific toolset is not found and the cxx command is detected. The 'cxx'  
toolset will use the CXX, CXXFLAGS, and LIBS environment variables, if present.  
  
Similarly, the cross-cxx toolset is available for cross-compiling by using the  
BUILD_CXX, BUILD_CXXFLAGS, and BUILD_LDFLAGS environment variables to compile  
binaries that will be executed on the build system. This allows CXX etc. to be  
set for cross-compilers to be propagated to subprocesses.  
  
  
Failed to build B2 build engine  
```  
  
I believe this issue is directly related to the problem discussed earlier in https://github.com/boostorg/build/issues/704#issuecomment-765558918

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-01-23 03:12:16 UTC  
> Url: https://github.com/boostorg/build/issues/705#issuecomment-765845331  

I believe, as I mentioned in #704, that it's an effect of error exit. And I found the like cause in the Bash POSIX shell mode docs:  
  
>  Enabling POSIX mode has the effect of setting the inherit_errexit option, so subshells spawned to execute command substitutions inherit the value of the -e option from the parent shell. When the inherit_errexit option is not enabled, Bash clears the -e option in such subshells.   
  
Hence if the enclosing CI shell does a `set -e` the `build.sh` will exit too early and fail to try other compilers past the first one. I'll  find a work around.

---

## Comment 2

> Username: mloskot  
> Created at: 2021-01-23 12:55:49 UTC  
> Url: https://github.com/boostorg/build/issues/705#issuecomment-765988476  

I have not looked at the script, but I have been bitten by the `set -e` lately, perhaps the `build.sh` suffers from similar Bash quirk:  
  
```  
$ cat bad.sh  
#!/bin/bash  
set -e  
echo "start"  
echo abc | grep xyz  
if [ $? -eq 0 ]; then  
    echo "found"  
fi  
echo "continue"  
  
$ ./bad.sh  
start  
```  
  
corrected to  
  
```  
$ cat good.sh  
#!/bin/bash  
set -e  
echo "start"  
if echo abc | grep xyz; then  
    echo "found"  
fi  
echo "continue"  
  
$ ./good.sh  
start  
continue  
```

---

## Comment 3

> Username: mloskot  
> Created at: 2021-01-23 14:42:10 UTC  
> Url: https://github.com/boostorg/build/issues/705#issuecomment-766089683  

I tried to look at the `src/engine/build.sh`, made this silly change  
  
```  
diff --git a/src/engine/build.sh b/src/engine/build.sh  
index e6fb0cf76..9d4aaf781 100755  
--- a/src/engine/build.sh  
+++ b/src/engine/build.sh  
@@ -101,6 +101,7 @@ test_uname ()  
 test_compiler ()  
 {  
     local EXE="${CXX:=$1}"  
+    echo "EXE=${EXE} 1=${1}"  
     local COMAND  
     shift  
     COMAND="${EXE} $@ ${CXXFLAGS:=}"  
```  
  
I may be missing something obvious, but I don't think there should be `EXE=g++` all the way through the toolset checks:  
  
```  
$  ./build.sh --guess-toolset  
EXE=g++ 1=g++  
EXE=g++ 1=clang++  
EXE=g++ 1=xlC_r  
EXE=g++ 1=pgc++  
EXE=g++ 1=pathCC  
EXE=g++ 1=como  
EXE=g++ 1=KCC  
EXE=g++ 1=bc++  
EXE=g++ 1=aCC  
EXE=g++ 1=/opt/SUNWspro/bin/CC  
EXE=g++ 1=cxx  
EXE=g++ 1=cpp  
EXE=g++ 1=CC  
EXE=g++ 1=cxx  
EXE=g++ 1=cxx  
EXE=g++ 1=cxx  
  
Could not find a suitable toolset.  
  
You can specify the toolset as the argument, i.e.:  
    ./build.sh gcc  
...  
```

---

## Comment 4

> Username: grafikrobot  
> Created at: 2021-01-23 14:49:07 UTC  
> Updated at: 2021-01-23 14:50:09 UTC  
> Url: https://github.com/boostorg/build/issues/705#issuecomment-766090724  

What I'm seeing locally is that things work fine without `--verbose` being enabled. Hence something in how `echo_run` is written that is causing issues. And, yes, what you're seeing can happen, as the script just tries every compiler it can think of. But it should be stopping when it finds one.  
  
Actually scratch that.. It also fails without verbose :-(

---

## Comment 5

> Username: grafikrobot  
> Created at: 2021-01-23 15:11:27 UTC  
> Url: https://github.com/boostorg/build/issues/705#issuecomment-766093907  

And.. I found the problem. And it's a horribly stupid mistake :-) This is the problem line:  
```  
    local EXE="${CXX:=$1}"  
```

---

## Comment 6

> Username: mloskot  
> Created at: 2021-01-23 15:30:45 UTC  
> Url: https://github.com/boostorg/build/issues/705#issuecomment-766096436  

It's fine but just for the first invocation.

---

## Comment 7

> Username: mloskot  
> Created at: 2021-01-23 22:45:41 UTC  
> Url: https://github.com/boostorg/build/issues/705#issuecomment-766194188  

@grafikrobot Thank you very much!

---

## Comment 8

> Username: mpesso1  
> Created at: 2022-10-21 11:26:09 UTC  
> Url: https://github.com/boostorg/build/issues/705#issuecomment-1286829001  

If running command in dir that requires admin, make sure to use sudo.  This also solved it for me.  
```  
sudo ./bootstrap.sh  
```
