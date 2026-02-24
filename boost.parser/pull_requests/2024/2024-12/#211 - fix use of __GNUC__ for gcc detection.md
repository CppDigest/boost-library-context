# #211 fix use of __GNUC__ for gcc detection [Closed]

> Username: Jannik2099  
> Created at: 2024-12-28 10:39:28 UTC  
> Updated at: 2025-02-21 07:54:36 UTC  
> Closed at: 2025-02-21 05:37:48 UTC  
> Url: https://github.com/boostorg/parser/pull/211  

`__GNUC__` is not useable to detect presence of gcc, nor has it ever been. It represents *the current version of the GNU C language extensions, as implemented by gcc of the respective major version*.  
  
Accordingly, clang defines `__GNUC__` to 5, which breaks a lot of your compiler checks.  
  
Detecting gcc is actually quite nontrivial as it doesn't expose a nice macro like clang does with `__clang__`, hence I used `BOOST_GCC` and `BOOST_LIBSTDCXX_VERSION` from `boost/config.hpp` accordingly.  
  
In some cases I was unsure if you encountered an issue with concepts support in old gcc, or incomplete ranges support in old libstdc++. Particularly in `replace` and `transform_replace`.

---

## Comment 1

> Username: Jannik2099  
> Created_at: 2024-12-28 10:46:40 UTC  
> Url: https://github.com/boostorg/parser/pull/211#issuecomment-2564293959  

Oh huh, I guess `<boost/config.hpp>` isn't available in the CI checkouts - I thought the repo would get merged into a full Boost tree.  
Any advice?

---

## Comment 2

> Username: tzlaine  
> Created_at: 2025-02-16 19:05:36 UTC  
> Url: https://github.com/boostorg/parser/pull/211#issuecomment-2661572973  

Thanks for looking into this, and sorry it has taken me so long to take up the issue.  
  
I'm not convinced this is a problem yet.  A grep for __GNUC__ in the boost tree yields over a thousand lines, whereas a grep for BOOST_GCC yields only about 350.  It looks like if this is a problem, it's already rife within Boost.  
  
More importantly, looking at the Boost config headers, I see this in select_compiler_config.hpp:  
  
```c++  
# elif defined(__GNUC__) && !defined(__ibmxl__)  
//  GNU C++:  
#   define BOOST_COMPILER_CONFIG "boost/config/compiler/gcc.hpp"  
```  
  
and this in boost/config/compiler/gcc.hpp:  
  
```c++  
#define BOOST_GCC_VERSION (__GNUC__ * 10000 + __GNUC_MINOR__ * 100 + __GNUC_PATCHLEVEL__)  
#if !defined(__CUDACC__)  
#define BOOST_GCC BOOST_GCC_VERSION  
#endif  
```  
  
IOW, BOOST_GCC is defined strictly in terms of __GNUC*__, with no attention paid at all to Clang.  Am I missing something?

---

## Comment 3

> Username: tzlaine  
> Created_at: 2025-02-16 19:06:40 UTC  
> Url: https://github.com/boostorg/parser/pull/211#issuecomment-2661573404  

Oh, and as for the broken tests, Parser is meant to be usable standalone.  If this is a problem that needs fixing, it will involve duplicating a small part of Boost.Config probably.

---

## Comment 4

> Username: Jannik2099  
> Created_at: 2025-02-17 20:01:44 UTC  
> Url: https://github.com/boostorg/parser/pull/211#issuecomment-2663983727  

> sorry it has taken me so long to take up the issue.  
  
no worries  
  
> A grep for GNUC in the boost tree yields over a thousand lines, whereas a grep for BOOST_GCC yields only about 350  
  
It's likely that a bunch of those places are erroneous, but I haven't looked into this at boost scale. I only discovered this in Parser by chance when I noticed that `static_assert_merge_attributes` was disabled even though I wasn't using gcc.  
  
I didn't spot any bug caused by this either, since you provided workarounds for older gcc versions in all cases.  
  
> IOW, BOOST_GCC is defined strictly in terms of GNUC*, with no attention paid at all to Clang. Am I missing something?  
  
Yes. This header only gets included if Boost.Config determines that this is not clang, see `boost/config/detail/select_compiler_config.hpp`  
  
Particularly  
  
```cpp  
#elif defined __clang__ && !defined(__ibmxl__) && !defined(__CODEGEARC__)  
//  Clang C++ emulates GCC, so it has to appear early.  
#   define BOOST_COMPILER_CONFIG "boost/config/compiler/clang.hpp"  
...  
# elif defined(__GNUC__) && !defined(__ibmxl__)  
//  GNU C++:  
#   define BOOST_COMPILER_CONFIG "boost/config/compiler/gcc.hpp"  
  
```  
  
> If this is a problem that needs fixing, it will involve duplicating a small part of Boost.Config probably.  
  
since we only need to disambiguate clang and gcc (no one else defines `__GNUC__` and can compile any C++ from this decade, afaik), that shouldn't be too ugly

---

## Comment 5

> Username: tzlaine  
> Created_at: 2025-02-21 05:37:48 UTC  
> Url: https://github.com/boostorg/parser/pull/211#issuecomment-2673513758  

Thanks for the explanation.  I get it now.  The fix I want to use is very different, because I uncovered a couple of other changes I wanted to make when I made the main change (__GNUC__ -> BOOST_PARSER_GCC).  So this PR is superseded by PR #214.

---
