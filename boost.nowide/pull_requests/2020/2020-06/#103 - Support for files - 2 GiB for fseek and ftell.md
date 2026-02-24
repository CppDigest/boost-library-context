# #103 Support for files > 2 GiB for fseek and ftell. [Merged]

> Username: davidwed  
> Created at: 2020-06-02 14:41:58 UTC  
> Updated at: 2020-06-08 08:53:14 UTC  
> Merged at: 2020-06-08 08:53:05 UTC  
> Closed at: 2020-06-08 08:53:05 UTC  
> Url: https://github.com/boostorg/nowide/pull/103  



---

## Comment 1

> Username: Flamefire  
> Created_at: 2020-06-02 15:08:16 UTC  
> Updated_at: 2020-06-02 15:10:01 UTC  
> Url: https://github.com/boostorg/nowide/pull/103#issuecomment-637604233  

Thanks for the PR! I was having this at the back of my mind for a while now.  
  
The alternative I considered was using the `std::filebuf` MSVC extension functions allowing `const wchar_t*` arguments when using VS, `ext/stdio_filebuf.h` otherwise on windows and `std::filebuf` for non-Windows. That would avoid the overhead of maintaining a custom filebuf just for the open function and benefit from improvements to the stdlib. However there is no solution for libc++ although it doesn't seem to be used on Windows  
  
Anyway this is a welcome improvement. To fix CI please add the macros to `include/.../config.hpp` too and instead of calling a platform specific `fseeko64` you can introduce a library specific macro like `BOOST_NOWIDE_FSEEK64` (and without the BOOST_ prefix in the standalone config). This avoids adding an unexpected macro in "global namespace"     
For the formatting: I like that style for indented macros but unfortunately clang-format has trouble doing that properly so it can't be used here. So just dedent those as suggested by CI

---

## Comment 2

> Username: Berrysoft  
> Created_at: 2020-06-03 01:44:23 UTC  
> Url: https://github.com/boostorg/nowide/pull/103#issuecomment-637903972  

> The alternative I considered...  
  
How about inherit `std::filebuf` and shadow the `open` function?

---

## Review 3 [Commented]

> Username: Flamefire  
> Created_at: 2020-06-03 07:03:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/nowide/pull/103#pullrequestreview-423270742  

📁 include/boost/nowide/config.hpp

```diff
  57 | #define BOOST_NOWIDE_FTELL64 ftello
  58 | #define BOOST_NOWIDE_FSEEK64 fseeko
  59 |+ #elif(defined(_MSC_VER) && defined(_MSC_VER) >= 1400)
```

> Username: Flamefire  
> Created_at: 2020-06-03 07:03:48 UTC  
> Updated_at: 2020-06-03 21:28:17 UTC  
> Url: https://github.com/boostorg/nowide/pull/103#discussion_r434350309  

I guess you meant to write ` && _MSC_VER >= 1400`, but you can omit this completetly as older MSVCs are not C++11 conformant enough anyway


---

## Review 4 [Commented]

> Username: Flamefire  
> Created_at: 2020-06-03 07:39:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/nowide/pull/103#pullrequestreview-423294097  

📁 include/boost/nowide/config.hpp

```diff
  52 | 
  53 |- #if(defined(__GNUC__) || defined(__MINGW32__) && !defined(__APPLE__))
  53 |+ #if(defined(__GNUC__) && !defined(__APPLE__) || defined(__MINGW32__))
```

> Username: Flamefire  
> Created_at: 2020-06-03 07:39:14 UTC  
> Updated_at: 2020-06-03 21:28:18 UTC  
> Url: https://github.com/boostorg/nowide/pull/103#discussion_r434368806  

To avoid ambiguous evaluation order you can use parantheses to group the sub-expressions too. Or as an even easier alternative: Move the `__APPLE__` branch to the top to avoid having to check for the !APPLE case.


---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2020-06-03 22:35:59 UTC  
> Updated_at: 2020-06-03 23:15:58 UTC  
> Url: https://github.com/boostorg/nowide/pull/103#issuecomment-638496830  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/103?src=pr&el=h1) Report  
> Merging [#103](https://codecov.io/gh/boostorg/nowide/pull/103?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/4d78b024d019175f8bf09dc662cd1fb8d0d84572&el=desc) will **increase** coverage by `19.00%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff              @@  
##           develop     #103       +/-   ##  
============================================  
+ Coverage    71.13%   90.13%   +19.00%       
============================================  
  Files           12       23       +11       
  Lines          582     2504     +1922       
  Branches       183      183                 
============================================  
+ Hits           414     2257     +1843       
- Misses          62      241      +179       
+ Partials       106        6      -100       
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/103?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `82.22% <100.00%> (+26.51%)` | :arrow_up: |  
| [src/cstdlib.cpp](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree#diff-c3JjL2NzdGRsaWIuY3Bw) | `91.37% <0.00%> (-8.63%)` | :arrow_down: |  
| [include/boost/nowide/args.hpp](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvYXJncy5ocHA=) | `97.18% <0.00%> (-2.82%)` | :arrow_down: |  
| [include/boost/nowide/convert.hpp](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvY29udmVydC5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [test/test\_iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2lvc3RyZWFtLmNwcA==) | `37.50% <0.00%> (ø)` | |  
| [test/test\_stdio.cpp](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3N0ZGlvLmNwcA==) | `98.76% <0.00%> (ø)` | |  
| [test/test\_env.cpp](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2Vudi5jcHA=) | `96.42% <0.00%> (ø)` | |  
| [test/test\_stackstring.cpp](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3N0YWNrc3RyaW5nLmNwcA==) | `100.00% <0.00%> (ø)` | |  
| [test/test\_fstream.cpp](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2ZzdHJlYW0uY3Bw) | `98.59% <0.00%> (ø)` | |  
| [test/test\_fstream\_cxx11.cpp](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2ZzdHJlYW1fY3h4MTEuY3Bw) | `100.00% <0.00%> (ø)` | |  
| ... and [14 more](https://codecov.io/gh/boostorg/nowide/pull/103/diff?src=pr&el=tree-more) | |

---

## Comment 6

> Username: Flamefire  
> Created_at: 2020-06-04 07:33:07 UTC  
> Url: https://github.com/boostorg/nowide/pull/103#issuecomment-638662594  

Good to see it passes now. I've noticed you removed the Cygwin variant. What was the reason? Don't the 64bit functions need to be used there too?

---

## Comment 7

> Username: davidwed  
> Created_at: 2020-06-04 07:37:53 UTC  
> Url: https://github.com/boostorg/nowide/pull/103#issuecomment-638665394  

As far as I have read, there are no *64 functions under cygwin, the types are switched depending on the environment (i686/x86_64).   
  
So only fseek / ftell is needed.

---

## Comment 8

> Username: Flamefire  
> Created_at: 2020-06-04 08:26:26 UTC  
> Url: https://github.com/boostorg/nowide/pull/103#issuecomment-638694203  

Google comes up with this: http://www.polarhome.com/service/man/?qf=fseeko64&tf=2&of=Cygwin&sf=3 but it seems to be quite complicated indeed. I found https://stackoverflow.com/questions/30867831/portable-support-for-large-files which quotes  
> 64 bit file access is the natural file access type for Cygwin. off_t is 8 bytes. There are no foo64 functions for that reason  
  
There is some sample code using `ftell` too on cygwin but IMO this is wrong as you'd need to use `off_t` but `ftell` uses `long` which may be 32 or 64 bit. So to me it appears that `fseeko` is the correct function but that is not available with `std=c++xx`. My solution was to put those non-standard function calls into a cpp file and define something to make them available w/o affecting user code.     
As for this macro-based solution I'm inclined to accept this for now.  
  
There also seems to be some issues with the `_fseeki64` function on Windows like https://stackoverflow.com/questions/4034227/on-windows-fseeki64-does-not-seek-to-seek-end-correctly-for-large-files and https://stackoverflow.com/a/30867832/1930508 which tells a story where `_ftelli64` returns a plain wrong result for a trivial example.  
  
Lastly: IIUC you have to open files via `fopen64` to be able to use the 64bit manipulation functions. Shouldn't this be done here too or does this not apply on Windows (Actually users are not supposed to use this class on non-Windows but for testing it might still be good. Not a bug though if it doesn't really work) I've seen https://gitlab.onelab.info/getdp/getdp/commit/9fdac97a32825dc9c033bfc4c6b38548a1de8ec6 using `_wfopen64` but haven't found it in official docs  
  
Anyway, tests are green, it looks like an improvement already so I'll merge this if you are good with that. I'll probably improve on this a bit later and add tests to avoid breaking on "obscure" platforms

---

## Comment 9

> Username: davidwed  
> Created_at: 2020-06-08 07:19:31 UTC  
> Url: https://github.com/boostorg/nowide/pull/103#issuecomment-640416277  

I don't really like the macro solution either. But from my point of view it is now working better than before. ( no more exception when a `seek()` is called on a file > 2 GiB ).

---

## Comment 10

> Username: Flamefire  
> Created_at: 2020-06-08 08:53:14 UTC  
> Url: https://github.com/boostorg/nowide/pull/103#issuecomment-640465694  

Alright, thanks!

---
