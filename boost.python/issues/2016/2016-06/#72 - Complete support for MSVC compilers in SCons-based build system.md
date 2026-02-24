# #72 - Complete support for MSVC compilers in SCons-based build system [Closed]

> Username: stefanseefeld  
> Created at: 2016-06-20 11:47:25 UTC  
> Updated at: 2018-04-09 01:30:05 UTC  
> Closed at: 2018-04-09 01:30:04 UTC  
> Url: https://github.com/boostorg/python/issues/72  

Support for MSVC is incomplete in the SCons-based build system. Figure out what is missing (presumably compiler flags) to make `scons config arch=x86` and `scons config arch=x86_64` work on Windows.

---

## Comment 1

> Username: cowo78  
> Created at: 2016-06-21 07:36:25 UTC  
> Updated at: 2016-06-21 07:37:55 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-227363324  

There are a couple of issues.  
First, in SConstruct the arch = ARGUMENTS.get('arch', platform.machine()) does not yield the architecture configured in bin.SCons/config.py but the host machine.  
Second, even if the TARGET_ARCH parameter is correctly passed to Environment() it does not really get into the SCons.Environment.Base. Base does not call ancestor **init**() thus the TARGET_ARCH kw is simply ignored instead of being copied into the _dict that in turn gets a copy of SCons.Defaults.ConstructionEnvironment.  
SCons 2.5.0, boost::python f7d7c54173

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2016-06-21 15:31:31 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-227477982  

Yes, I'm aware about the 'arch' handling. To make a long story short, it's complicated. (It's a chicken-and-egg problem of initializing the Variables object and the Environment.) As a quick (hopefully temporary) workaround you'll have to pass the 'arch' variable each time you invoke `scons`, if you want a non-default target.  
  
For the second part, are you referring to the comment in Environment.py:909 (https://bitbucket.org/scons/scons/src/4e1b77a684f40c9df0d66bd2da415815c436f6e3/src/engine/SCons/Environment.py?at=default&fileviewer=file-view-default#Environment.py-909:912) ?  
I assumed at that point TARGET_ARCH had already been evaluated (i.e., the MSVC toolchain been initialized).  
But this is worth verifying...

---

## Comment 3

> Username: cowo78  
> Created at: 2016-06-22 07:17:58 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-227662813  

No problem with the workaround, only it's not enough. More info below.  
For the second part yes, saw the comment and went through the code. The _dict attribute always gets defaults, regardless of the keywords passed. Basically it's missing a self._dict.update(kw).  
Cannot attach the logfile, but see [the log on pastebin](http://pastebin.com/zA2a0DXZ) and trace when Environment is created. Most of the times TARGET_ARCH is not passed, thus it probably gets the default. Ok, maybe this is more for SCons developers, if you don't want to dive into this we can go on and try with my default arch (amd64)

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2016-06-22 15:08:29 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-227774578  

OK, it's possible you have run into a SCons bug, in which case I think you may want to file a bug report there (or, help me write it).  
Meanwhile, let's try to get things working with the default arch.

---

## Comment 5

> Username: cowo78  
> Created at: 2016-06-24 13:23:01 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-228343984  

Here's the results for default amd64 architecture on windows 7, python 2.7.11 64bit, visual studio 2015 (MSVC 19).  
  
Commandline:  
`C:\Python27\scons.bat arch=amd64 test`  
  
Scons config:  
`CPPPATH = ['c:\\Python27\\include', 'c:\\Devel\\boost_1_61_0']  
CCFLAGS = ['/nologo']  
LIBPATH = ['c:\\Python27\\libs']  
LIBS = ['python27']  
PYTHON = 'c:\\Python27\\python.exe'  
PYTHONLIBS = ['python27']  
arch = 'amd64'  
toolchain = 'msvc'`  
  
Test Summary  
74 tests; 72 pass; 2 fails  
`bin.SCons\msvc-14.0\release\dynamic\threading-multi\test\long.result  
Result: fail  
Output: Traceback (most recent call last):  
  File "bin.SCons\msvc-14.0\release\dynamic\threading-multi\test\long.py", line 4, in <module>  
    from past.builtins import long  
ImportError: No module named past.builtins`  
  
This is OK, I'm running python2.  
  
`bin.SCons\msvc-14.0\release\dynamic\threading-multi\test\import_.result  
Result: fail  
Output: ImportError: No module named site`  
  
Did not investigate, but the module is there and is importable by 2.7.11 interpreter.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2016-06-24 20:48:28 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-228458130  

Thanks, this looks encouraging. Unfortunately I'm not quite there yet. For the 32-bit build on WIndows I still get many failures (24 passes, 55 failures), most of them with an error  
  
  `ImportError: DLL load failed: The specified module could not be found.`  
  
which apparently means some dependencies couldn't be resolved at import time. Any idea how to debug this, i.e. how to figure out what symbol(s) or libraries couldn't be found ? (In any case, seeing that this works for you suggests that this is a platform issue, not an actual test failure.)  
  
Thanks !

---

## Comment 7

> Username: cowo78  
> Created at: 2016-06-27 06:30:39 UTC  
> Updated at: 2016-06-27 06:32:26 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-228664004  

Maybe it's just that the ld (whatever is on win32) can't find the boost_python shared object. In my case I did:  
`set PATH=%PATH%;c:\Devel\python\bin.SCons\msvc-14.0\release\dynamic\threading-multi\src`  
In this case it's some whole library missing, not a single symbol. Use this to look into the COFF dependencies: [dependency walker](http://www.dependencywalker.com/)

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2016-06-27 13:49:22 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-228750692  

Indeed, it was just that ! Thanks for the tip ! :-)  
Now I only need to figure out how to fix the SCons logic to do that automatically.

---

## Comment 9

> Username: cowo78  
> Created at: 2016-07-07 12:21:33 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-231061977  

Anything else I can do?

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2016-07-07 13:58:15 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-231085405  

In fact, I believe this issue is solved. Thanks for your help !

---

## Comment 11

> Username: stefanseefeld  
> Created at: 2016-07-16 04:29:47 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-233107506  

Hi @cowo78 ,  
  
today I started working on Appveyor CI support for Boost.Python, resulting in build (test) failures I'm unable to reproduce locally (see https://ci.appveyor.com/project/stefanseefeld/boost-python).  
Perhaps you have some idea what may be going on there ? (I have tried building both 64-bit as well as 32-bit variants, producing the same failure.)

---

## Comment 12

> Username: cowo78  
> Created at: 2016-07-19 08:10:26 UTC  
> Updated at: 2016-07-19 08:26:37 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-233560906  

I'm not getting the same results with python 2.7.11, boost 1.61 includes, latest develop branch, msvc14 and amd64.  
My understanding is that the linker optimized away the synthetized implementation for `boost::get_pointer<struct test_class<0> const volatile >`  
Talking about WHY this happens, ehm...  
OTOH I'm getting compilation errors on `calling_conventions` tests, looking into that.  
Compilation errors appear only using amd64 target, not x86.

---

## Comment 13

> Username: stefanseefeld  
> Created at: 2016-07-19 13:34:16 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-233633898  

Yeah, I'm seeing these calling_convention errors, too. It looks as if some of the different attributes map to the same code, resulting into these "...already defined" errors. But that's just a shot in the dark.  
  
As to the compilation error: Could it be that the Appveyor setup uses a slighly different toolchain version than the one we were testing with ? There must be something explaining the difference. It would be good to have a way to record the precise toolchain version being used, so we have something to compare.  
(In addition, I'd like to look into support for explicitly requesting a specific toolchain via `scons config toolchain=...`, so have a little more control over the process, rather than simply relying on whatever scons picks by default.)

---

## Comment 14

> Username: cowo78  
> Created at: 2016-07-19 14:26:21 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-233649660  

The precise compiler release can be seen on the output if you remove the `nologo` option from the command line. Of course it's a default option hardcoded into scons.  
Anyway, I'm running `Microsoft (R) C/C++ Optimizing Compiler Version 19.00.23506 for x64` which is visual studio 14.  
The supported vs packages are in SCons/Tool/MSCommon/vs.py, how to request a specific version is beyond my knowledge.  
Since AppVeyor installation is outputting to msvc14 I guess it's a Community or Express something edition. I expected the compiler to be exactly the same...

---

## Comment 15

> Username: cowo78  
> Created at: 2016-07-21 07:25:42 UTC  
> Updated at: 2016-07-21 08:37:31 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-234177475  

Compilation errors arise by simply defining one of `BOOST_PYTHON_ENABLE_*`  before including `boost/python.hpp`, see #77

---

## Comment 16

> Username: stefanseefeld  
> Created at: 2018-04-09 01:30:04 UTC  
> Url: https://github.com/boostorg/python/issues/72#issuecomment-379601960  

The scons-based build logic has been obsoleted by a new [Faber](http://stefan.seefeld.name/faber/)-based build system.
