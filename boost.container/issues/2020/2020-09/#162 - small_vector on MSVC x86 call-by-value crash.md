# #162 - small_vector on MSVC x86 call-by-value crash [Closed]

> Username: tobias-loew  
> Created at: 2020-09-02 05:15:03 UTC  
> Updated at: 2020-12-12 18:15:39 UTC  
> Closed at: 2020-11-02 10:24:56 UTC  
> Url: https://github.com/boostorg/container/issues/162  

Hi,  
using small_vector on MSCV (e.g. VS 2019) with x86 (Win32) results in a crash when trying to free the small_vector in the called function.  
The reason is (referring to the following example): the callee "main" copy-constructs a copy of `m` and forwards its address to `foo`. In the preamble of `foo` the content pointed-to by the argument is mem-copied(!!!) to the local storage of `n`.  
This mem-copying destroys the invariants of the small_vector, as the address to the stored data is not adjusted.  
  
I'm not sure, what other platforms are affected by this issue.  
  
best  
Tobias  
  
```  
#include <boost/container/small_vector.hpp>  
  
using vec_t = boost::container::small_vector<  
    double,  
    13  
    >;  
  
void foo(vec_t n) {  
}  
  
int main()  
{  
    vec_t m;  
    foo(m);  
    return 0;  
}  
```

---

## Comment 1

> Username: tobias-loew  
> Created at: 2020-09-02 18:03:11 UTC  
> Url: https://github.com/boostorg/container/issues/162#issuecomment-685903885  

I think I found out what's going wrong. It's the way MSVC is achieving alignment for x86 (NOT x86-64) code.  
  
The callee does not assume that the arguments on the heap are aligned correctly, so for types with alignment bigger than 4, it allocates additional space and uses memcpy to copy the data into place - which obviously destroys the internal state of a small_vector when it is in "small"-state.  
  
An interesting fact is that the MSVC-compiler doesn't seem to trust itself, as it aligns the data also on the caller-site. So, the memcpy for alignment by the callee (which is the cause for the crash) is unnecessary.   
(But of course, the compiler couldn't know that for calls from other modules or for exported functions.)  
  
I'll report this to Microsoft.  
  
Tobias

---

## Comment 2

> Username: tobias-loew  
> Created at: 2020-09-05 08:13:06 UTC  
> Updated at: 2020-09-09 11:51:28 UTC  
> Url: https://github.com/boostorg/container/issues/162#issuecomment-687570319  

Hi,  
  
I think I found ways, how to work around  the faulty MSVC x86 ABI. (The following refers all to that ABI)  
  
First of all: for a sufficiently complex class type `S` (as far as I found out: not an aggregate) adding `alignas(N)` yields different asm-code for call-by-value function preambles. (i.e. the additional memcpy to an aligned address)   
  
And that happens **even when** `N` is the natural alignment of `S`, i.e. `N  == alignof(S)`.  
  
Furthermore, `alignas` is viral: if any member or base uses `alignas` it is implicitly assumed for the whole class.  
  
There are multiple ways to tackle this problem for MSVC x86:  
  
- ~changing the way `small_vector` recognizes smallness and how it addresses the small-memory: if small_vector addresses its small-memory _relative_ (instead of _absolute_), then it would be agnostic to its location (as long as it has the correct alignment). MSVC's std::basic_string implementation uses that approach and therefore doesn't suffer from the ABI-flaw.~  
Nope, `small_vector` already addresses its small memory relatively. The problem is the `m_start` in the underlying `vector_alloc_holder`, and this cannot be changed without affecting all the other vector-containers too.  
  
  
- ~using the `align_storage` implementation from Boost.Type-Traits instead of the version from Boost.Move: the Type-Traits-version tries to achieve alignment using builtin-types whenever possible, and thus injects an `alingas(N)` only when absolutely necessary (while the Boost.Move version **always** adds `alignas(N)`, even for `char`).  This would not only solve the problem for natural aligned types but, also remove the performance penalty of the additional memcpy in function preamble, which currently always happens when transferring a small_vector by value.~  
And this will also not work: Boost.Type-Traits' approach simply won't align call-by-value parameters.  
  
So, currently I don't see a way to use types with alignment > 4 that are not mem-movable with MSVC x86 call-by-value semantics.  
  
Tobias

---

## Comment 3

> Username: igaztanaga  
> Created at: 2020-11-02 10:24:56 UTC  
> Url: https://github.com/boostorg/container/issues/162#issuecomment-720381523  

I've made a special version for MSVC X86 avoiding alignas up to alignment== 8 using builtin types, which is enough for non-overaligned types (your example compiles fine with the modification). For overaligned types it seems there is no solution. The commit is:  
  
https://github.com/boostorg/move/commit/3446ceeaafaf5788cf53140394ccd9e5652c9577  
  
I'm closing the issue, as it seems we can't do more to fix it from the library side.

---

## Comment 4

> Username: tobias-loew  
> Created at: 2020-11-02 10:37:15 UTC  
> Url: https://github.com/boostorg/container/issues/162#issuecomment-720387972  

Thanks a lot. Microsoft also announced to fix the alignment problem for x86 in the next minor visual studio release (hopefully in 16.8 or 16.9).

---

## Comment 5

> Username: tobias-loew  
> Created at: 2020-11-16 09:50:17 UTC  
> Updated at: 2020-11-16 09:51:00 UTC  
> Url: https://github.com/boostorg/container/issues/162#issuecomment-727866776  

The issue shall be resolved in VS 2019 16.9 Preview 2 - so hopefully in all VS 2019 16.9 (non-preview) and above.  
https://developercommunity.visualstudio.com/comments/1209850/view.html

---

## Comment 6

> Username: tobias-loew  
> Created at: 2020-12-12 18:14:07 UTC  
> Updated at: 2020-12-12 18:15:39 UTC  
> Url: https://github.com/boostorg/container/issues/162#issuecomment-743794651  

I tested with VS 2019 16.9 Preview 2 and boost 1.74 and it is no longer an issue!  
So, we can expect that it will also work with all VS 16.9 and later.
