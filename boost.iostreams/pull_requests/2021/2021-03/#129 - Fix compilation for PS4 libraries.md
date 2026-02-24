# #129 Fix compilation for PS4 libraries [Open]

> Username: pkierski  
> Created at: 2021-03-17 17:51:14 UTC  
> Updated at: 2021-03-18 05:30:24 UTC  
> Url: https://github.com/boostorg/iostreams/pull/129  

This is a simple compatibility fix for PS4 standard libraries. They don't have Dinkumware-like `fpos_t` implementation. The minimal code to reproduce problem/check solution:  
  
```  
#include <boost/iostreams/categories.hpp>  
#include <boost/iostreams/operations.hpp>  
  
class SourceMock  
{  
public:  
	using char_type = char;  
	using category = boost::iostreams::source_tag;  
	std::streamsize read(char* /*buffer*/, std::streamsize /*size*/) { return 0; }  
};  
  
void foo()  
{  
	SourceMock source;  
	char buffer[42];  
	boost::iostreams::read(source, buffer, sizeof(buffer));  
}  
```  
  
Output without fix looks like:  
```  
1>In file included from foo.ccp:2:  
1>In file included from C:\.conan\7fa4fb\1\include\boost/iostreams/operations.hpp:16:  
1>In file included from C:\.conan\7fa4fb\1\include\boost/iostreams/close.hpp:19:  
1>In file included from C:\.conan\7fa4fb\1\include\boost/iostreams/detail/adapter/non_blocking_adapter.hpp:13:  
1>In file included from C:\.conan\7fa4fb\1\include\boost/iostreams/seek.hpp:23:  
1>C:\.conan\7fa4fb\1\include\boost/iostreams/positioning.hpp(52,10): error : no matching constructor for initialization of 'std::streampos' (aka 'fpos<_Mbstatet>')  
1>C:\Program Files (x86)\SCE\ORBIS SDKs\7.000\target\include\iosfwd(48,2): note: candidate constructor not viable: no known conversion from 'boost::iostreams::stream_offset' (aka 'long') to 'fpos_t' for 2nd argument  
1>C:\Program Files (x86)\SCE\ORBIS SDKs\7.000\target\include\iosfwd(43,2): note: candidate constructor not viable: allows at most single argument '_Off', but 2 arguments were provided  
1>C:\Program Files (x86)\SCE\ORBIS SDKs\7.000\target\include\iosfwd(38,8): note: candidate constructor (the implicit copy constructor) not viable: requires 1 argument, but 2 were provided  
1>C:\Program Files (x86)\SCE\ORBIS SDKs\7.000\target\include\iosfwd(38,8): note: candidate constructor (the implicit move constructor) not viable: requires 1 argument, but 2 were provided  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-03-18 05:26:07 UTC  
> Updated_at: 2021-03-18 05:30:24 UTC  
> Url: https://github.com/boostorg/iostreams/pull/129#issuecomment-801637363  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/129?src=pr&el=h1) Report  
> Merging [#129](https://codecov.io/gh/boostorg/iostreams/pull/129?src=pr&el=desc) (e30b6e2) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/f4ea3c9c0ab1667add847ad9438621f63073a061?el=desc) (f4ea3c9) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/129/graphs/tree.svg?width=650&height=150&src=pr&token=LBEfwtNfca)](https://codecov.io/gh/boostorg/iostreams/pull/129?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #129   +/-   ##  
========================================  
  Coverage    68.84%   68.84%             
========================================  
  Files           80       80             
  Lines         3444     3444             
  Branches      1027     1027             
========================================  
  Hits          2371     2371             
  Misses         454      454             
  Partials       619      619             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/129?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/129?src=pr&el=footer). Last update [4d2e763...e30b6e2](https://codecov.io/gh/boostorg/iostreams/pull/129?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
