# #99 Support systems without implicit include path [Merged]

> Username: Flamefire  
> Created at: 2022-07-02 13:30:28 UTC  
> Updated at: 2025-01-06 11:32:31 UTC  
> Merged at: 2022-07-06 12:12:22 UTC  
> Closed at: 2022-07-06 12:12:23 UTC  
> Url: https://github.com/boostorg/locale/pull/99  

To support e.g. recent Cygwins where relative paths to the current file are not automatically resolved  
move the sources into a `boost/locale` subfolder and add `src` to the private include paths.  
This then allows to include those files as `"boost/locale/foo/bar.hpp"`.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-07-02 18:22:08 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1172940376  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/99?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#99](https://codecov.io/gh/boostorg/locale/pull/99?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (41868c6) into [develop](https://codecov.io/gh/boostorg/locale/commit/e24241ee6764e010ca3c3586e188d1258650c4d4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e24241e) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #99   +/-   ##  
========================================  
  Coverage    80.97%   80.97%             
========================================  
  Files           76       76             
  Lines         5891     5891             
========================================  
  Hits          4770     4770             
  Misses        1121     1121             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/99?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/encoding/codepage.cpp](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9jb2RlcGFnZS5jcHA=) | `92.50% <ø> (ø)` | |  
| [src/boost/locale/encoding/conv.hpp](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9jb252LmhwcA==) | `88.88% <ø> (ø)` | |  
| [src/boost/locale/encoding/iconv\_codepage.ipp](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9pY29udl9jb2RlcGFnZS5pcHA=) | `91.66% <ø> (ø)` | |  
| [src/boost/locale/posix/codecvt.cpp](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9jb2RlY3Z0LmNwcA==) | `84.42% <ø> (ø)` | |  
| [src/boost/locale/posix/collate.cpp](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9jb2xsYXRlLmNwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/posix/converter.cpp](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9jb252ZXJ0ZXIuY3Bw) | `87.09% <ø> (ø)` | |  
| [src/boost/locale/posix/numeric.cpp](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9udW1lcmljLmNwcA==) | `71.85% <ø> (ø)` | |  
| [src/boost/locale/posix/posix\_backend.cpp](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9wb3NpeF9iYWNrZW5kLmNwcA==) | `88.88% <ø> (ø)` | |  
| [src/boost/locale/shared/date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZGF0ZV90aW1lLmNwcA==) | `67.94% <ø> (ø)` | |  
| [src/boost/locale/shared/format.cpp](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZm9ybWF0LmNwcA==) | `0.00% <ø> (ø)` | |  
| ... and [26 more](https://codecov.io/gh/boostorg/locale/pull/99/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/99?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/99?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e24241e...41868c6](https://codecov.io/gh/boostorg/locale/pull/99?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-07-03 09:19:00 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1173044799  

@artyom-beilis Objections?

---

## Comment 3

> Username: jwakely  
> Created_at: 2023-01-13 09:09:22 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1381513097  

> To support e.g. recent Cygwins where relative paths to the current file are not automatically resolved  
  
Could you clarify this? When did Cygwin stop supporting relative paths for headers?!

---

## Comment 4

> Username: Flamefire  
> Created_at: 2023-01-13 09:47:13 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1381554126  

> Could you clarify this? When did Cygwin stop supporting relative paths for headers?!  
  
Not sure about the "when" but we have a CI job for the latest 64bit Cygwin on Appveyor ("Visual Studio 2022" image at `C:\cygwin64\bin` with GCC 11) where it failed due to this issue. I didn't bother to find the exact version of Cygwin and/or GCC but as `#include "foo"` may or may not search others paths than those given by `-I` according to the GCC manual I decided on this solution.  
  
A failing job due to this is e.g. https://ci.appveyor.com/project/Flamefire/locale/builds/44056107/job/gwot235bxj994epq

---

## Comment 5

> Username: jwakely  
> Created_at: 2023-01-13 10:14:49 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1381607547  

> but as `#include "foo"` may or may not search others paths than those given by `-I` according to the GCC manual  
  
I don't know where you read that, but that's not really what it says. The manual is clear that `#include "foo"` searches:  
  
> first relative to the directory of the current file, and then in a preconfigured list of standard system directories. [...] There are a number of command-line options you can use to add additional directories to the search path. The most commonly-used option is -Idir, which causes dir to be searched after the current directory (for the quote form of the directive) and ahead of the standard system directories. You can specify multiple -I options on the command line, in which case the directories are searched in left-to-right order.   
  
https://gcc.gnu.org/onlinedocs/gcc-12.2.0/cpp/Search-Path.html  
  
There is no "may or may not". It's precisely defined.  
  
And this change still uses the `#include "..."` form, so you didn't actually change that anyway, it still does the same thing. Except now the first search (relative to the directory of the current file) will always fail, and so the headers will always be found in the directories specified by `-I` instead.  
  
As the failing job shows, you *already* had `-I"."` in the compilation options, so even if it was failing to search relative to the current file, it should have found the headers via the `-I"."` option instead.  
  
I think you have misunderstood something about the root cause of the failures, as this is not due to any change in Cygwin or GCC. Maybe there was a problem with the filesystem on the tester machine. I wouldn't care, but the churn caused by this change causes unnecessary work for downstream packagers to adjust.

---

## Comment 6

> Username: jwakely  
> Created_at: 2023-01-13 10:20:21 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1381616287  

> I think you have misunderstood something about the root cause of the failures  
  
In particular, the title of this PR is "Support systems without implicit include path". While the C++ standard permits such a system to exist, I am not aware of any real C++ compilers that work that way. GCC on Cygwin is certainly not a "system without implicit include path".

---

## Comment 7

> Username: Flamefire  
> Created_at: 2023-01-13 12:14:10 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1381772244  

> I don't know where you read that, but that's not really what it says.  
  
I can't remember unfortunately. However it is at least what I observed. But you can [find e.g.](https://commandlinefanatic.com/cgi-bin/showarticle.cgi?article=art026)  
  
> The first was to enclose the name of the header file in double quotes, e.g. "header.h". In this case, the compiler should (typically) look for the header in the same directory as the source file.  
  
Note the "typically"!  
  
> And this change still uses the #include "..." form, so you didn't actually change that anyway, it still does the same thing. Except now the first search (relative to the directory of the current file) will always fail, and so the headers will always be found in the directories specified by -I instead.  
  
Yes I'm using that form to differentiate between "public" and "private" headers, i.e. in `include` vs `src`  
  
> As the failing job shows, you already had -I"." in the compilation options, so even if it was failing to search relative to the current file, it should have found the headers via the -I"." option instead.  
  
No. `-I"."` searches the **current**  dir not the one relative to the source file.  
  
`"g++"   <...>  -I"."  -c -o "bin.v2\libs\locale\build\gcc-11\rls\cxstd-14-iso\lnk-sttc\trgt-os-cygwn\thrd-mlt\vsblt-hdn\win32\lcid.o" "libs\locale\src\win32\lcid.cpp"`  
  
As you can see `g++` is executed from the BOOST_ROOT and hence the header in `libs\locale\src\win32` is not found.  
  
> In particular, the title of this PR is "Support systems without implicit include path". While the C++ standard permits such a system to exist, I am not aware of any real C++ compilers that work that way. GCC on Cygwin is certainly not a "system without implicit include path".  
  
I'm sorry for the work this has caused but there obviously exists such a system and this particular Cygwin version on Appveyor is one of them. IIRC I had verified this on a local Windows machine. Other Boost libs have also encountered this especially in their test files which could often be solved by adding the `test` folder to the includes. Due to the "private headers" used here it was more complicated to resolve.  
  
May I ask what difficulties arose from this change?

---

## Comment 8

> Username: jwakely  
> Created_at: 2023-01-13 12:23:33 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1381781357  

> Note the "typically"!  
  
That's quoting K&R and isn't talking about GCC. The GCC manual explains how GCC behaves.  
  
> As you can see g++ is executed from the BOOST_ROOT and hence the header in libs\locale\src\win32 is not found.  
  
Ah yes, I didn't notice that the compiler wasn't being run in the same dir as the source, sorry.  
  
> May I ask what difficulties arose from this change?  
  
When the locations of all the source files change, any downstream patches carried by distros need to be rebased manually. When that renaming was done for some curious claim about GCC not supporting relative includes, I get interested!

---

## Comment 9

> Username: jwakely  
> Created_at: 2023-01-13 12:31:38 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1381789490  

> When that renaming was done for some curious claim about GCC not supporting relative includes, I get interested!  
  
Because if that really happened, it's a serious bug that needs to be fixed. But I don't see any relevant bug reports or fixes in GCC.

---

## Comment 10

> Username: Flamefire  
> Created_at: 2023-01-13 15:36:49 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1382027887  

> any downstream patches carried by distros need to be rebased manually. When that renaming was done for some curious claim about GCC not supporting relative includes, I get interested!  
> Because if that really happened, it's a serious bug that needs to be fixed. But I don't see any relevant bug reports or fixes in GCC.  
  
Sorry for that. If those patches are applicable in general a PR is welcome so less of them are required.  
  
I could not find a definitive source on how GCC on Cygwin is meant to behave and as the standard allows "systems without implicit include paths" it is possible that either the GCC documentation is outdated or it is meant to behave like that on Cygwin or it is indeed a bug. So I had to do this patch to improve compatibility.  
  
I can double check if I find anything which may help here but as this is now included in 2 Boost releases already I figure reverting the change (if another solution was found) would cause the same trouble again, wouldn't it?

---

## Comment 11

> Username: jwakely  
> Created_at: 2023-01-13 15:54:11 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1382046908  

> I could not find a definitive source on how GCC on Cygwin is meant to behave  
  
I already linked to the GCC preprocessor manual, which applies on Cygwin just as much as every other target. That's definitive. It's not outdated.  
  
> I figure reverting the change (if another solution was found) would cause the same trouble again, wouldn't it?  
  
Indeed.

---

## Comment 12

> Username: Flamefire  
> Created_at: 2023-01-15 12:23:36 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-1383137601  

Ok I did a few more experiments with Cygwin on Windows:  
  
```  
$ touch bar/foo.h  
$ cat bar/main.cpp  
#include "foo.h"  
int main(){}  
```  
  
- `g++ "bar/main.cpp"` works  
- `g++ "bar\main.cpp"` fails to find "foo.h"  
  
This is either from the Cygwin MSYS shell/bash as well as from Windows cmd (which is what is used on CI). Looks like there is a bug in GCC related to path separators even on GCC 11.3 which is the latest currently available for Cygwin.  
This could be workarounded in B2 I guess which means the change can be reverted which would simplify the folder structure and includes. But also cause the same trouble for package maintainers again. So I'd leave it as-is unless there is interest in reverting it.

---

## Comment 13

> Username: Flamefire  
> Created_at: 2025-01-02 12:59:14 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-2567739189  

> When the locations of all the source files change, any downstream patches carried by distros need to be rebased manually. When that renaming was done for some curious claim about GCC not supporting relative includes, I get interested!  
  
As I just found another case where this renaming and way of including is causing issues I'm thinking about reverting this.  
  
@jwakely Do you have examples of patches that would need to be adjusted? Do they still apply in the current version or are/can they be dropped already? I'm trying to determine the impact of this.

---

## Comment 14

> Username: jwakely  
> Created_at: 2025-01-06 11:32:10 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-2572924216  

I don't think there are any patches in the current Fedora RPM that would need to be changed if you reverted this.

---

## Comment 15

> Username: jwakely  
> Created_at: 2025-01-06 11:32:31 UTC  
> Url: https://github.com/boostorg/locale/pull/99#issuecomment-2572924787  

(thanks for checking though!)

---
