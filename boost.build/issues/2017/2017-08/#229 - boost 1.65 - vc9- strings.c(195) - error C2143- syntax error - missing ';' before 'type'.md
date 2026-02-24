# #229 - boost 1.65 / vc9: strings.c(195) : error C2143: syntax error : missing ';' before 'type' [Closed]

> Username: jschueller  
> Created at: 2017-08-22 18:18:46 UTC  
> Updated at: 2017-12-07 06:27:35 UTC  
> Closed at: 2017-09-06 12:09:35 UTC  
> Url: https://github.com/boostorg/build/issues/229  

when updating our recipe to build boost from 1.64 to 1.65 with vc9 I got this failure during bootstrap.bat:  
  
  
```  
(C:\bld\boost-cpp_1503417954088\_b_env) C:\bld\boost-cpp_1503417954088\work\boost_1_65_0>call bootstrap.bat   
Building Boost.Build engine  
Failed to build Boost.Build engine.  
Please consult bootstrap.log for further diagnostics.  
You can try to obtain a prebuilt binary from  
   http://sf.net/project/showfiles.php?group_id=7586&package_id=72941  
Also, you can file an issue at http://svn.boost.org   
Please attach bootstrap.log in that case.  
###  
### Using 'vc14' toolset.  
###  
(C:\bld\boost-cpp_1503417954088\_b_env) C:\bld\boost-cpp_1503417954088\work\boost_1_65_0\tools\build\src\engine>if exist bootstrap rd /S /Q bootstrap   
(C:\bld\boost-cpp_1503417954088\_b_env) C:\bld\boost-cpp_1503417954088\work\boost_1_65_0\tools\build\src\engine>md bootstrap   
(C:\bld\boost-cpp_1503417954088\_b_env) C:\bld\boost-cpp_1503417954088\work\boost_1_65_0\tools\build\src\engine>cl /nologo /RTC1 /Zi /MTd /Fobootstrap/ /Fdbootstrap/ -DNT -DYYDEBUG -wd4996 kernel32.lib advapi32.lib user32.lib /Febootstrap\jam0  command.c compile.c constants.c debug.c execcmd.c execnt.c filent.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathnt.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c md5.c class.c cwd.c w32_getreg.c native.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c   
command.c  
compile.c  
constants.c  
debug.c  
execcmd.c  
execnt.c  
filent.c  
frames.c  
function.c  
glob.c  
hash.c  
hdrmacro.c  
headers.c  
jam.c  
jambase.c  
jamgram.c  
lists.c  
make.c  
make1.c  
object.c  
Generating Code...  
Compiling...  
option.c  
output.c  
parse.c  
pathnt.c  
pathsys.c  
regexp.c  
rules.c  
scan.c  
search.c  
subst.c  
timestamp.c  
variable.c  
modules.c  
strings.c  
strings.c(195) : error C2143: syntax error : missing ';' before 'type'  
strings.c(196) : error C2065: 'p' : undeclared identifier  
strings.c(196) : error C2065: 'p' : undeclared identifier  
strings.c(196) : warning C4047: '>=' : 'int' differs in levels of indirection from 'char *'  
strings.c(196) : error C2065: 'p' : undeclared identifier  
strings.c(196) : error C2100: illegal indirection  
strings.c(196) : error C2065: 'p' : undeclared identifier  
strings.c(196) : error C2100: illegal indirection  
strings.c(196) : error C2065: 'p' : undeclared identifier  
strings.c(196) : error C2100: illegal indirection  
strings.c(196) : error C2106: '=' : left operand must be l-value  
strings.c(239) : error C2143: syntax error : missing ';' before 'type'  
strings.c(240) : error C2275: 'string' : illegal use of this type as an expression  
        c:\bld\boost-cpp_1503417954088\work\boost_1_65_0\tools\build\src\engine\strings.h(22) : see declaration of 'string'  
strings.c(240) : error C2146: syntax error : missing ';' before identifier 'bar_copy'  
strings.c(240) : error C2065: 'bar_copy' : undeclared identifier  
strings.c(240) : error C2109: subscript requires array or pointer type  
strings.c(241) : error C2065: 'bar_copy' : undeclared identifier  
strings.c(241) : warning C4047: 'function' : 'string *' differs in levels of indirection from 'int'  
strings.c(241) : warning C4024: 'string_copy' : different types for formal and actual parameter 1  
strings.c(241) : error C2065: 'bar' : undeclared identifier  
strings.c(241) : warning C4047: 'function' : 'const char *' differs in levels of indirection from 'int'  
strings.c(241) : warning C4024: 'string_copy' : different types for formal and actual parameter 2  
strings.c(242) : error C2065: 'bar_copy' : undeclared identifier  
strings.c(242) : warning C4047: 'function' : 'string *' differs in levels of indirection from 'int'  
strings.c(242) : warning C4024: 'string_rtrim' : different types for formal and actual parameter 1  
strings.c(243) : error C2065: 'bar_copy' : undeclared identifier  
strings.c(243) : error C2223: left of '->value' must point to struct/union  
strings.c(243) : error C2198: 'strcmp' : too few arguments for call  
strings.c(245) : error C2065: 'bar_copy' : undeclared identifier  
strings.c(245) : warning C4047: 'function' : 'string *' differs in levels of indirection from 'int'  
strings.c(245) : warning C4024: 'string_rtrim' : different types for formal and actual parameter 1  
strings.c(246) : error C2065: 'bar_copy' : undeclared identifier  
strings.c(246) : error C2223: left of '->value' must point to struct/union  
strings.c(246) : error C2198: 'strcmp' : too few arguments for call  
filesys.c  
builtins.c  
md5.c  
class.c  
cwd.c  
w32_getreg.c  
Generating Code...  
Compiling...  
native.c  
set.c  
path.c  
regex.c  
property-set.c  
sequence.c  
order.c  
Generating Code...  
```  
  
  
see complete log at https://ci.appveyor.com/project/conda-forge/boost-cpp-feedstock/build/1.0.32/job/4g6u93b4aaekksnc

---

## Comment 1

> Username: minhnguyenerp  
> Created at: 2017-08-31 22:53:38 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-326441668  

I got the same error. 'vc11' toolset

---

## Comment 2

> Username: antony593  
> Created at: 2017-09-04 06:15:42 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-326874835  

I got the same error , help

---

## Comment 3

> Username: rbhkamal  
> Created at: 2017-09-05 19:00:37 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-327271391  

Same here with V1_65_0, compiling on windows 10 with Visual Studio 2010 SP1. Same thing wasn't happening in V1_64_0

---

## Comment 4

> Username: grafikrobot  
> Created at: 2017-09-05 19:25:02 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-327277486  

Do you get the error with the current "develop" state?

---

## Comment 5

> Username: jschueller  
> Created at: 2017-09-05 19:30:41 UTC  
> Updated at: 2017-09-05 19:31:34 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-327278938  

maybe fixed by https://github.com/boostorg/build/pull/226 ?

---

## Comment 6

> Username: grafikrobot  
> Created at: 2017-09-05 19:32:07 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-327279307  

@jschueller Probably.. Which is why I was asking if it worked on develop branch.

---

## Comment 7

> Username: rbhkamal  
> Created at: 2017-09-05 19:42:22 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-327281930  

I just grabbed the latest release from boost.org. I didn't try any  
development branch.  
  
On Sep 5, 2017 3:32 PM, "Rene Rivera" <notifications@github.com> wrote:  
  
@jschueller <https://github.com/jschueller> Probably.. Which is why I was  
asking if it worked on develop branch.  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub  
<https://github.com/boostorg/build/issues/229#issuecomment-327279307>, or mute  
the thread  
<https://github.com/notifications/unsubscribe-auth/AEmVu0FAPr_spczlIxmY8hL3YQEAAX_kks5sfaHEgaJpZM4O-_83>  
.

---

## Comment 8

> Username: grafikrobot  
> Created at: 2017-09-05 19:49:21 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-327283661  

Try grabbing one of the 1.66 snapshots from here https://dl.bintray.com/boostorg/develop/ to see if this is already fixed or not.

---

## Comment 9

> Username: minhnguyenerp  
> Created at: 2017-09-06 04:12:36 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-327369368  

@grafikrobot its fixed in 1.66 snapshots

---

## Comment 10

> Username: jschueller  
> Created at: 2017-09-06 07:26:32 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-327398902  

it seems to go fine with #226 + #227 applied on top of 1.65

---

## Comment 11

> Username: LuanBaviloni  
> Created at: 2017-12-07 06:24:40 UTC  
> Updated at: 2017-12-07 06:27:35 UTC  
> Url: https://github.com/boostorg/build/issues/229#issuecomment-349875108  

For VS2012, replace these files in folder \tools\build\src\engine\:  
  
- debugger.c  
- strings.c  
  
[debugger.c.txt](https://github.com/boostorg/build/files/1538001/debugger.c.txt)  
[strings.c.txt](https://github.com/boostorg/build/files/1538002/strings.c.txt)
