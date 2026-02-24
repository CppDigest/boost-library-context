# #100 Use full ios include in filebuf.hpp [Merged]

> Username: davidwed  
> Created at: 2020-05-29 18:25:19 UTC  
> Updated at: 2020-06-02 08:54:00 UTC  
> Merged at: 2020-06-02 08:54:00 UTC  
> Closed at: 2020-06-02 08:54:00 UTC  
> Url: https://github.com/boostorg/nowide/pull/100  

Without the missing include there are compiler errors with the standalone version.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-29 19:42:35 UTC  
> Updated_at: 2020-06-02 07:46:27 UTC  
> Url: https://github.com/boostorg/nowide/pull/100#issuecomment-636153217  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/100?src=pr&el=h1) Report  
> Merging [#100](https://codecov.io/gh/boostorg/nowide/pull/100?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/540bd86cf9f0fb71ea2b94ae7dc404940ec2de27&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #100      +/-   ##  
===========================================  
- Coverage    90.13%   90.13%   -0.01%       
===========================================  
  Files           23       23                
  Lines         2505     2504       -1       
  Branches       183      183                
===========================================  
- Hits          2258     2257       -1       
  Misses         241      241                
  Partials         6        6                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/100?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/100/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `82.22% <ø> (ø)` | |  
| [test/test\_codecvt.cpp](https://codecov.io/gh/boostorg/nowide/pull/100/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2NvZGVjdnQuY3Bw) | `94.76% <0.00%> (-0.03%)` | :arrow_down: |

---

## Comment 2

> Username: Flamefire  
> Created_at: 2020-05-29 20:20:38 UTC  
> Url: https://github.com/boostorg/nowide/pull/100#issuecomment-636174406  

Thanks for the PR. Can you tell me what the error message is? I'd like to come up with a test so this doesn't happen again. And if that is the right header to include here then I thing the iosfwd include can be removed. But I'd like to double-check why it doesn't come up on CI.

---

## Comment 3

> Username: davidwed  
> Created_at: 2020-05-29 21:28:38 UTC  
> Url: https://github.com/boostorg/nowide/pull/100#issuecomment-636200278  

Here are some details:  
  
build nowide standalone v10.0.2 ( https://github.com/boostorg/nowide/releases/download/v10.0.2/nowide_standalone_v10.0.2.tar.gz )  
   
OS: Windows 8.1  
Build Env: Msys2  
Compiler: gcc version 10.1.0 (Rev2, Built by MSYS2 project)  
   
Cmake Build parameters:  
cmake .. -G"Unix Makefiles" -DCMAKE_INSTALL_PREFIX:PATH="$baseDir/nowide/debug" -DCMAKE_BUILD_TYPE:STRING="Debug" -DBUILD_TESTING:BOOL="1"  
   
Make output:  
   
$ make  
[  3%] Building CXX object CMakeFiles/nowide.dir/src/cstdio.cpp.obj  
[  7%] Building CXX object CMakeFiles/nowide.dir/src/cstdlib.cpp.obj  
[ 10%] Building CXX object CMakeFiles/nowide.dir/src/iostream.cpp.obj  
[ 14%] Linking CXX static library libnowide.a  
[ 14%] Built target nowide  
[ 17%] Building CXX object test/CMakeFiles/nowide-test_codecvt.dir/test_codecvt.cpp.obj  
[ 21%] Linking CXX executable ../nowide-test_codecvt.exe  
[ 21%] Built target nowide-test_codecvt  
[ 25%] Building CXX object test/CMakeFiles/nowide-test_convert.dir/test_convert.cpp.obj  
[ 28%] Linking CXX executable ../nowide-test_convert.exe  
[ 28%] Built target nowide-test_convert  
[ 32%] Building CXX object test/CMakeFiles/nowide-test_env_win.dir/test_env.cpp.obj  
[ 35%] Linking CXX executable ../nowide-test_env_win.exe  
[ 35%] Built target nowide-test_env_win  
[ 39%] Building CXX object test/CMakeFiles/nowide-test_env.dir/test_env.cpp.obj  
[ 42%] Linking CXX executable ../nowide-test_env.exe  
[ 42%] Built target nowide-test_env  
[ 46%] Building CXX object test/CMakeFiles/nowide-test_fstream.dir/test_fstream.cpp.obj  
In file included from [BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/fstream.hpp:12,  
                 from [BaseDir]/nowide_build/nowide_standalone_v10.0.2/test/test_fstream.cpp:10:  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp:451:19: error: invalid use of incomplete type 'std::ios' {aka 'class std::basic_ios<char>'}  
  451 |         std::ios::openmode mode_;  
      |                   ^~~~~~~~  
In file included from C:/msys64/mingw64/include/c++/10.1.0/iterator:64,  
                 from [BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/detail/convert.hpp:13,  
                 from [BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/convert.hpp:11,  
                 from [BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/stackstring.hpp:11,  
                 from [BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp:15,  
                 from [BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/fstream.hpp:12,  
                 from [BaseDir]/nowide_build/nowide_standalone_v10.0.2/test/test_fstream.cpp:10:  
C:/msys64/mingw64/include/c++/10.1.0/iosfwd:77:11: note: declaration of 'std::ios' {aka 'class std::basic_ios<char>'}  
   77 |     class basic_ios;  
      |           ^~~~~~~~~  
In file included from [BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/fstream.hpp:12,  
                 from [BaseDir]/nowide_build/nowide_standalone_v10.0.2/test/test_fstream.cpp:10:  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp: In constructor 'nowide::basic_filebuf<char>::basic_filebuf()':  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp:62:13: error: class 'nowide::basic_filebuf<char>' does not have any field named 'mode_'  
   62 |             mode_(std::ios_base::openmode(0))  
      |             ^~~~~  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp: In member function 'void nowide::basic_filebuf<char>::swap(nowide::basic_filebuf<char>&)':  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp:98:18: error: 'mode_' was not declared in this scope; did you mean 'mode_t'?  
   98 |             swap(mode_, rhs.mode_);  
      |                  ^~~~~  
      |                  mode_t  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp:98:29: error: 'class nowide::basic_filebuf<char>' has no member named 'mode_'  
   98 |             swap(mode_, rhs.mode_);  
      |                             ^~~~~  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp: In member function 'nowide::basic_filebuf<char>* nowide::basic_filebuf<char>::open(const wchar_t*, std::ios_base::openmode)':  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp:154:13: error: 'mode_' was not declared in this scope; did you mean 'mode_t'?  
  154 |             mode_ = mode;  
      |             ^~~~~  
      |             mode_t  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp: In member function 'nowide::basic_filebuf<char>* nowide::basic_filebuf<char>::close()':  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp:168:13: error: 'mode_' was not declared in this scope; did you mean 'mode_t'?  
  168 |             mode_ = std::ios_base::openmode(0);  
      |             ^~~~~  
      |             mode_t  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp: In member function 'virtual int nowide::basic_filebuf<char>::overflow(int)':  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp:219:18: error: 'mode_' was not declared in this scope; did you mean 'mode_t'?  
  219 |             if(!(mode_ & std::ios_base::out))  
      |                  ^~~~~  
      |                  mode_t  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp: In member function 'virtual int nowide::basic_filebuf<char>::underflow()':  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp:274:18: error: 'mode_' was not declared in this scope; did you mean 'mode_t'?  
  274 |             if(!(mode_ & std::ios_base::in))  
      |                  ^~~~~  
      |                  mode_t  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp: In member function 'virtual int nowide::basic_filebuf<char>::pbackfail(int)':  
[BaseDir]/nowide_build/nowide_standalone_v10.0.2/include/nowide/filebuf.hpp:298:18: error: 'mode_' was not declared in this scope; did you mean 'mode_t'?  
  298 |             if(!(mode_ & std::ios_base::in))  
      |                  ^~~~~  
      |                  mode_t  
make[2]: *** [test/CMakeFiles/nowide-test_fstream.dir/build.make:84: test/CMakeFiles/nowide-test_fstream.dir/test_fstream.cpp.obj] Fehler 1  
make[1]: *** [CMakeFiles/Makefile2:1057: test/CMakeFiles/nowide-test_fstream.dir/all] Fehler 2  
make: *** [Makefile:161: all] Fehler 2

---

## Review 4 [Commented]

> Username: Flamefire  
> Created_at: 2020-05-30 10:54:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/nowide/pull/100#pullrequestreview-421396825  

📁 include/boost/nowide/filebuf.hpp

```diff
  17 | #include <cstdio>
  18 |+ #include <ios>
  19 | #include <iosfwd>
```

> Username: Flamefire  
> Created_at: 2020-05-30 10:54:05 UTC  
> Updated_at: 2020-06-02 06:00:06 UTC  
> Url: https://github.com/boostorg/nowide/pull/100#discussion_r432831760  

Can you remove the iosfwd include please?


---

## Review 5 [Changes requested]

> Username: Flamefire  
> Created_at: 2020-05-30 10:55:00 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/nowide/pull/100#pullrequestreview-421396873  

Ok, I could reproduce this on CI: https://github.com/boostorg/nowide/runs/722914533?check_suite_focus=true  
  
I'm also adding a CI check for MSYS on windows with the standalone version: https://github.com/boostorg/nowide/pull/101  
  
So I'd only like to ask you to remove the now unnecessary iosfwd include. Thank you for your help!

---

## Review 6 [Approved]

> Username: Flamefire  
> Created_at: 2020-06-02 08:53:17 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/nowide/pull/100#pullrequestreview-422473846  

Thank you!

---
