# #94 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Merged]

> Username: eldiener  
> Created at: 2020-04-17 17:07:49 UTC  
> Updated at: 2020-08-25 16:30:11 UTC  
> Merged at: 2020-08-25 16:30:11 UTC  
> Closed at: 2020-08-25 16:30:11 UTC  
> Url: https://github.com/boostorg/program_options/pull/94  



---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-06 12:36:26 UTC  
> Url: https://github.com/boostorg/program_options/pull/94#issuecomment-624623255  

A code coverage check failed with:  
  
```  
+lcov --version  
  
lcov: LCOV version 1.14-6-g40580cd  
  
+lcov --gcov-tool=gcov-7 --rc lcov_branch_coverage=1 --base-directory /home/travis/build/boostorg/boost-root/libs/program_options --directory /home/travis/build/boostorg/boost-root --capture --output-file all.info  
  
Capturing coverage data from /home/travis/build/boostorg/boost-root  
  
Can't locate PerlIO/gzip.pm in @INC (you may need to install the PerlIO::gzip module) (@INC contains: /etc/perl /usr/local/lib/perl/5.18.2 /usr/local/share/perl/5.18.2 /usr/lib/perl5 /usr/share/perl5 /usr/lib/perl/5.18 /usr/share/perl/5.18 /usr/local/lib/site_perl .) at /usr/local/bin/geninfo line 62.  
  
BEGIN failed--compilation aborted at /usr/local/bin/geninfo line 62.  
```  
  
This has nothing to with this PR. Can this please be merged ?

---

## Comment 2

> Username: pdimov  
> Created_at: 2020-08-24 13:11:09 UTC  
> Url: https://github.com/boostorg/program_options/pull/94#issuecomment-679116887  

The change from `abort` to `std::abort` seems wrong; `std::abort` is defined in `<ctsdlib>`, whereas `<stdlib.h>`, which is being included, defines `abort`.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-08-24 15:58:10 UTC  
> Updated_at: 2020-08-25 14:42:53 UTC  
> Url: https://github.com/boostorg/program_options/pull/94#issuecomment-679214371  

# [Codecov](https://codecov.io/gh/boostorg/program_options/pull/94?src=pr&el=h1) Report  
> Merging [#94](https://codecov.io/gh/boostorg/program_options/pull/94?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/program_options/commit/d95d31684832075fda04c9fc916f8ec875552763?el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/program_options/pull/94/graphs/tree.svg?width=650&height=150&src=pr&token=UMdM0EgHK3)](https://codecov.io/gh/boostorg/program_options/pull/94?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #94   +/-   ##  
========================================  
  Coverage    49.89%   49.89%             
========================================  
  Files           23       23             
  Lines         1385     1385             
  Branches       707      707             
========================================  
  Hits           691      691             
- Misses         110      111    +1       
+ Partials       584      583    -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/program_options/pull/94?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [src/cmdline.cpp](https://codecov.io/gh/boostorg/program_options/pull/94/diff?src=pr&el=tree#diff-c3JjL2NtZGxpbmUuY3Bw) | `45.51% <ø> (ø)` | |  
| [src/convert.cpp](https://codecov.io/gh/boostorg/program_options/pull/94/diff?src=pr&el=tree#diff-c3JjL2NvbnZlcnQuY3Bw) | `65.62% <ø> (ø)` | |  
| [src/parsers.cpp](https://codecov.io/gh/boostorg/program_options/pull/94/diff?src=pr&el=tree#diff-c3JjL3BhcnNlcnMuY3Bw) | `45.61% <ø> (ø)` | |  
| [src/options\_description.cpp](https://codecov.io/gh/boostorg/program_options/pull/94/diff?src=pr&el=tree#diff-c3JjL29wdGlvbnNfZGVzY3JpcHRpb24uY3Bw) | `48.26% <0.00%> (-0.39%)` | :arrow_down: |  
| [...de/boost/program\_options/detail/value\_semantic.hpp](https://codecov.io/gh/boostorg/program_options/pull/94/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvZGV0YWlsL3ZhbHVlX3NlbWFudGljLmhwcA==) | `43.75% <0.00%> (+1.56%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/program_options/pull/94?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/program_options/pull/94?src=pr&el=footer). Last update [a237942...e9d423c](https://codecov.io/gh/boostorg/program_options/pull/94?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 4 [Changes requested]

> Username: vprus  
> Created_at: 2020-08-24 16:19:42 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/program_options/pull/94#pullrequestreview-473655033  

Thanks for the patch! The main chance seems straightforward, but looks some some unrelated cleanups got added?

📁 test/winmain.cpp

```diff
  15 | 
  18 |- void check_equal(const std::vector<string>& actual, char **expected, int n)
  16 |+ void check_equal(const std::vector<std::string>& actual, char **expected, int n)
```

> Username: vprus  
> Created_at: 2020-08-24 16:18:58 UTC  
> Updated_at: 2020-08-25 14:42:47 UTC  
> Url: https://github.com/boostorg/program_options/pull/94#discussion_r475733554  

Is this change necessary to make borland work?

---

📁 test/winmain.cpp

```diff
  40 |     char* BOOST_PP_CAT(e, __LINE__)[] = expected;\
  43 |-     vector<string> BOOST_PP_CAT(v, __LINE__) = split_winmain(input);\
  41 |+     std::vector<std::string> BOOST_PP_CAT(v, __LINE__) = split_winmain(input);\
```

> Username: vprus  
> Created_at: 2020-08-24 16:19:08 UTC  
> Updated_at: 2020-08-25 14:42:47 UTC  
> Url: https://github.com/boostorg/program_options/pull/94#discussion_r475733650  

Likewise.

> Username: eldiener  
> Created_at: 2020-08-24 18:36:39 UTC  
> Updated_at: 2020-08-25 14:42:47 UTC  
> Url: https://github.com/boostorg/program_options/pull/94#discussion_r475817091  

Please, this is C++. You really want to have 'using namespace std;', include C++ and C header files, and leave off the std:: namespaces everywhere you can ? All that together can lead to problems, and did with the Embarcadero C++ compiler. The code already produces warnings on the invocation of TEST, which I did not bother to change, trying to keep my changes to a minimum. How am I hurting your code by providing the std:: namespace excplicitly ? It is not breaking anything and is certainly avoiding any other potential problems.

> Username: vprus  
> Created_at: 2020-08-24 19:18:03 UTC  
> Updated_at: 2020-08-25 14:42:47 UTC  
> Url: https://github.com/boostorg/program_options/pull/94#discussion_r475839072  

All this is valid C++ that no other compiler had problem with for a decade. I think it's reasonable to ask what specific problem Borland had here

> Username: eldiener  
> Created_at: 2020-08-25 00:47:28 UTC  
> Updated_at: 2020-08-25 14:42:47 UTC  
> Url: https://github.com/boostorg/program_options/pull/94#discussion_r476011975  

The specific problem Embarcadero had is that there is a `std::byte` ( https://en.cppreference.com/w/cpp/types/byte ) and there is an unqualified `byte` in the Windows header file rpcndr.h. Having `using namespace std;` creates the clash. Before you ask why Embarcadero encounters this problem while other compilers do not, let me answer that I did not want to spend hours trying to figure this out rather than spend minutes fixing it with perfectly reasonable code which changes nothing as far as the logic goes. Really Vladimir, you know as well as I do that specifying `using namespace std;` is generally a poor choice for anything but the simplest code which #includes very little and using any Boost library's code is never that simple.

> Username: eldiener  
> Created_at: 2020-08-25 14:44:49 UTC  
> Updated_at: 2020-08-25 14:44:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/94#discussion_r476505898  

I fixed the test/winmain.cpp in a better way without the previous changes I had made.


---

## Review 5 [Approved]

> Username: vprus  
> Created_at: 2020-08-25 16:29:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/program_options/pull/94#pullrequestreview-474652318  

Thanks again for the patch, it looks good.

---
