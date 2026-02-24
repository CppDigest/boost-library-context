# #5 add a --with-toolset=xxx command-line parameter to bootstrap.bat [Closed]

> Username: ericniebler  
> Created at: 2013-12-07 00:40:06 UTC  
> Updated at: 2017-10-30 16:01:14 UTC  
> Closed at: 2017-10-30 16:01:14 UTC  
> Url: https://github.com/boostorg/boost/pull/5  

per the "bootstrap.bat broken for MinGW since 1.55" thread on boost-devel.

---

## Comment 1

> Username: jmichae3  
> Created_at: 2014-02-10 12:24:51 UTC  
> Updated_at: 2014-02-10 12:42:06 UTC  
> Url: https://github.com/boostorg/boost/pull/5#issuecomment-34625749  

using gcc causes errors like "ar.h" not found and other linux-specific OS kinds of errors that are not provided on windows.  
it would be very nice to have install instructions for mingw-w64, since it's a different compiler than mingw, it has an MSYS but it also provides -m32 and -m64 for building 32 and 64-bit binaries. am currently using the experimental sjlj posix 4.9.0 version.. someone said they werre able to build mingw-w64 with MSYS, but didn't say how..  
  
from /prj/boost/boost_1_55_0/tools/build/v2:  
$ cat bootstrap.log  
###$ cat bootstrap.log  
###  
  
### Using 'gcc' toolset.  
  
###  
rm -rf bootstrap  
mkdir bootstrap  
gcc -o bootstrap/jam0 command.c compile.c constants.c debug.c execcmd.c frames.c  
 function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c  
 make.c make1.c object.c option.c output.c parse.c pathsys.c regexp.c rules.c sc  
an.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c built  
ins.c class.c cwd.c native.c md5.c w32_getreg.c modules/set.c modules/path.c mod  
ules/regex.c modules/property-set.c modules/sequence.c modules/order.c execunix.  
c fileunix.c pathunix.c  
builtins.c:34:23: fatal error: sys/wait.h: No such file or directory  
 # include <sys/wait.h>  
                       ^  
compilation terminated.  
execunix.c:20:26: fatal error: sys/resource.h: No such file or directory  
 #include <sys/resource.h>  
                          ^  
compilation terminated.  
fileunix.c:96:17: fatal error: ar.h: No such file or directory  
 # include <ar.h>  
                 ^  
compilation terminated.

---

## Comment 2

> Username: jmichae3  
> Created_at: 2014-02-13 11:21:28 UTC  
> Url: https://github.com/boostorg/boost/pull/5#issuecomment-34969577  

if you didn't know this about the more appropriate .cmd files (which have more commands and a better feature set and uses the cmd shell), you can escape certain (but not all) characters like > < | with a preceding ^  
the cmd shell is broken in this respect, submitted a bug report ot microsoft, but nothing done about it so far that I know of yet. maybe enough people didn't complain.  
.cmd also handles long filenames up to 260 chars and commandlines about 8192 characters in size.  
as far as .bat files goes, I am wondering if ^ works with .bat, but I have no usable command.com on x64 windows to check it with. I am also wondering if DJGPP (a gnu compiler set for DOS) could possibly build boost. it has a memory limitation less than 4GiB I think due to DPMI, and does not run on x64 windows without a virtual machine.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-26 14:21:50 UTC  
> Url: https://github.com/boostorg/boost/pull/5#issuecomment-339682230  

@danieljames should this PR be closed?  It is targeting the wrong branch, and I'm not sure if the solution is valid.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-10-30 16:01:14 UTC  
> Url: https://github.com/boostorg/boost/pull/5#issuecomment-340492293  

Closing in favor of #170.

---
