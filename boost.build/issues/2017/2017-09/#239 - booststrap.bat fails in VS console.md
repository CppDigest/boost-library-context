# #239 - booststrap.bat fails in VS console [Closed]

> Username: chfast  
> Created at: 2017-09-21 15:18:08 UTC  
> Updated at: 2021-06-26 04:15:39 UTC  
> Closed at: 2021-06-26 04:15:39 UTC  
> Url: https://github.com/boostorg/build/issues/239  

The `bootstrap.bat` fails to build the tools if preceded with   
```  
"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" x64  
```  
  
The log is:  
```  
###  
### Using 'vc14' toolset.  
###  
  
C:\.hunter\_Base\8fe144d\2fea431\3fd9419\Build\Boost\Source\tools\build\src\engine>if exist bootstrap rd /S /Q bootstrap   
  
C:\.hunter\_Base\8fe144d\2fea431\3fd9419\Build\Boost\Source\tools\build\src\engine>md bootstrap   
  
C:\.hunter\_Base\8fe144d\2fea431\3fd9419\Build\Boost\Source\tools\build\src\engine>cl /nologo /RTC1 /Zi /MTd /Fobootstrap/ /Fdbootstrap/ -DNT -DYYDEBUG -wd4996 kernel32.lib advapi32.lib user32.lib /Febootstrap\jam0  command.c compile.c constants.c debug.c execcmd.c execnt.c filent.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathnt.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c md5.c class.c cwd.c w32_getreg.c native.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c   
command.c  
compile.c  
constants.c  
debug.c  
execcmd.c  
execnt.c  
filent.c  
filent.c(488): warning C4477: 'sprintf' : format string '%.*s' requires an argument of type 'int', but variadic argument 1 has type '__int64'  
filent.c(488): note: this argument is used as a precision  
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
  
C:\.hunter\_Base\8fe144d\2fea431\3fd9419\Build\Boost\Source\tools\build\src\engine>.\bootstrap\jam0 -f build.jam --toolset=vc14 "--toolset-root=C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\Tools\..\..\VC\ "  clean   
build.jam:192: in module scope  
*** argument error  
* rule toolset ( name command .type ? : opt.out + : opt.define * : flags * : linklibs * )  
* called with: ( cc C:/Program Files (x86)/Microsoft Visual Studio 14.0/VC/bin/amd64/cl.exe : -o  : -D : -s -O :  )  
* extra argument (x86)/Microsoft  
build.jam:156:see definition of rule 'toolset' being called  
  
C:\.hunter\_Base\8fe144d\2fea431\3fd9419\Build\Boost\Source\tools\build\src\engine>.\bootstrap\jam0 -f build.jam --toolset=vc14 "--toolset-root=C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\Tools\..\..\VC\ "    
build.jam:192: in module scope  
*** argument error  
* rule toolset ( name command .type ? : opt.out + : opt.define * : flags * : linklibs * )  
* called with: ( cc C:/Program Files (x86)/Microsoft Visual Studio 14.0/VC/bin/amd64/cl.exe : -o  : -D : -s -O :  )  
* extra argument (x86)/Microsoft  
build.jam:156:see definition of rule 'toolset' being called  
  
C:\.hunter\_Base\8fe144d\2fea431\3fd9419\Build\Boost\Source\tools\build\src\engine>exit /b 1   
```

---

## Comment 1

> Username: nzbart  
> Created at: 2018-11-07 21:02:30 UTC  
> Url: https://github.com/boostorg/build/issues/239#issuecomment-436776160  

This bug appears to still be present, and the pull-request above was reverted. Is there any chance of having it resolved?

---

## Comment 2

> Username: LeeRuns  
> Created at: 2018-11-14 23:43:52 UTC  
> Updated at: 2018-11-14 23:44:01 UTC  
> Url: https://github.com/boostorg/build/issues/239#issuecomment-438860931  

has this been resolved?

---

## Comment 3

> Username: pdimov  
> Created at: 2019-03-24 17:21:41 UTC  
> Url: https://github.com/boostorg/build/issues/239#issuecomment-475980186  

See #415.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:11 UTC  
> Url: https://github.com/boostorg/build/issues/239#issuecomment-859203417  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 5

> Username: grafikrobot  
> Created at: 2021-06-26 04:15:39 UTC  
> Url: https://github.com/boostorg/build/issues/239#issuecomment-868944786  

Fixed in current B2.
