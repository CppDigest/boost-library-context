# #18 Explicitly cast to char * to appease clang-tidy [Closed]

> Username: tonyelewis  
> Created at: 2021-01-02 11:05:23 UTC  
> Updated at: 2021-01-02 18:14:58 UTC  
> Closed at: 2021-01-02 18:14:58 UTC  
> Url: https://github.com/boostorg/assert/pull/18  

The motivation for this change is that running:  
  
~~~cpp  
#include <iostream>  
  
#include <boost/assert/source_location.hpp>  
  
int main() {  
    ::std::cerr << BOOST_CURRENT_LOCATION << "\n";  
}  
~~~  
  
&hellip;through `clang-tidy` with `-checks=hicpp-no-array-decay` (eg see [this Godbolt](https://godbolt.org/z/Md8qcY)) gives:  
  
~~~no-highlight  
<source>:6:20: warning: do not implicitly decay an array into a pointer; consider using gsl::array_view or an explicit cast instead [hicpp-no-array-decay]  
    ::std::cerr << BOOST_CURRENT_LOCATION << "\n";  
                   ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/assert/source_location.hpp:89:79: note: expanded from macro 'BOOST_CURRENT_LOCATION'  
#  define BOOST_CURRENT_LOCATION ::boost::source_location(__FILE__, __LINE__, BOOST_CURRENT_FUNCTION)  
                                                                              ^  
/opt/compiler-explorer/libs/boost_1_75_0/boost/current_function.hpp:37:33: note: expanded from macro 'BOOST_CURRENT_FUNCTION'  
# define BOOST_CURRENT_FUNCTION __PRETTY_FUNCTION__  
                                ^  
28 warnings generated.  
Suppressed 27 warnings (27 in non-user code).  
Use -header-filter=.* to display errors from all non-system headers. Use -system-headers to display errors from system headers as well.  
~~~  
  
This also manifests as a `clang-tidy` warning in code that uses `BOOST_THROW_EXCEPTION`.  
  
This change silences the warning.

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-01-02 14:27:03 UTC  
> Url: https://github.com/boostorg/assert/pull/18#issuecomment-753479937  

Would https://github.com/boostorg/assert/commit/9004ce05539abe099cc27f78090fcda4872400ec work for you?

---

## Comment 2

> Username: tonyelewis  
> Created_at: 2021-01-02 14:38:53 UTC  
> Url: https://github.com/boostorg/assert/pull/18#issuecomment-753481399  

I'm 100% happy with the east-const and the change to the formatting.  
  
Were I maintaining this repo, I'd prefer to keep the extra line of comment that gestures to the `static_cast` and saves readers from having to follow a link and read a bug report just to learn that the point is to silence a spurious clang-tidy warning. But if you prefer to keep it terse, that's fine by me. :slightly_smiling_face:

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-01-02 15:06:20 UTC  
> Url: https://github.com/boostorg/assert/pull/18#issuecomment-753484479  

My question was more about whether the above commit silences clang-tidy properly.

---

## Comment 4

> Username: tonyelewis  
> Created_at: 2021-01-02 15:27:21 UTC  
> Url: https://github.com/boostorg/assert/pull/18#issuecomment-753486688  

Ah - sorry - I didn't look at the diff carefully enough. No, sorry, it doesn't work (but does if I explicitly define `__clang_tidy` on the command line).

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-01-02 15:36:10 UTC  
> Url: https://github.com/boostorg/assert/pull/18#issuecomment-753487633  

So clang-tidy does not predefine this macro? A Github search led me to https://github.com/GrieferAtWork/KOSmk4/blob/5e0b2561c411bf1221abe8d2f251c613b53f0004/kos/include/kos/anno.h#L63 from which I deduced that it would.

---

## Comment 6

> Username: tonyelewis  
> Created_at: 2021-01-02 15:51:48 UTC  
> Url: https://github.com/boostorg/assert/pull/18#issuecomment-753489492  

I'm afraid not.  Here's a Godbolt using clang-tidy to illustrate : https://godbolt.org/z/n46W35

---

## Comment 7

> Username: pdimov  
> Created_at: 2021-01-02 15:55:08 UTC  
> Url: https://github.com/boostorg/assert/pull/18#issuecomment-753489840  

Second try: https://github.com/boostorg/assert/commit/835f8db241676ab019389ac50aee31f29c831d66

---

## Comment 8

> Username: tonyelewis  
> Created_at: 2021-01-02 16:08:53 UTC  
> Url: https://github.com/boostorg/assert/pull/18#issuecomment-753491296  

Yep - that works for me.  
  
I'd say that it looks like `__clang_analyzer__` has only been defined in clang-tidy since May 2018 ( https://reviews.llvm.org/D46325 ). Out of interest: what's the motivation to avoid `static_cast`ing generally (less repetition; gets cast to `char const *` anyway)?  
  
Either way, I'm happy with this. Thanks.

---

## Comment 9

> Username: pdimov  
> Created_at: 2021-01-02 16:27:13 UTC  
> Url: https://github.com/boostorg/assert/pull/18#issuecomment-753493880  

It's a compromise between the reluctance to pollute the code with workarounds for clang-tidy's developers' stubborn resistance to fix their obvious false positive (https://reviews.llvm.org/D88833), and the desire to not make your life harder than necessary because you can do nothing about it.

---

## Comment 10

> Username: tonyelewis  
> Created_at: 2021-01-02 16:36:50 UTC  
> Url: https://github.com/boostorg/assert/pull/18#issuecomment-753495275  

I see. I think to my eye, the `#elif` approach is exacerbating the pollution more than mitigating it. But I can understand why you see it differently. And it's not my call. :slightly_smiling_face:   
  
Anyway, thanks very much for tackling this.

---
