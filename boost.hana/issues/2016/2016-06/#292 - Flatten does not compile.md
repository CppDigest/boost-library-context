# #292 - [c++1z][flatten] Flatten does not compile. [Closed]

> Username: ricejasonf  
> Created at: 2016-06-27 21:40:38 UTC  
> Updated at: 2017-09-13 16:46:16 UTC  
> Closed at: 2016-06-29 01:19:46 UTC  
> Url: https://github.com/boostorg/hana/issues/292  

In clang 3.9 (trunk) using c++1z `hana::flatten` does not compile with the following use case:  
  
``` cpp  
#include<boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
  
int main() {  
  constexpr auto xs = hana::flatten(hana::make_tuple(  
    hana::make_tuple(0, 1),  
    hana::make_tuple(3)  
  ));  
}  
```  
  
with error message:  
  
```  
/usr/local/include/boost/hana/detail/unpack_flatten.hpp:29:31: error: constexpr variable 'flat_length' must be initialized by a constant expression  
        static constexpr auto flat_length =  
                              ^  
```  
  
It can also be reproduced in `test.tuple.laws.functor`.  
  
I tried it out with an older build of clang 3.8 which does not reproduce the error. I don't know if it is a clang bug, or perhaps Boost.Hana is exploiting non-standard behaviour that is now fixed in clang.  
  
Let me know if there is an obvious workaround that I could employ in my fork. :grin:

---

## Comment 1

> Username: ldionne  
> Created at: 2016-06-28 00:32:33 UTC  
> Updated at: 2016-06-28 00:33:05 UTC  
> Url: https://github.com/boostorg/hana/issues/292#issuecomment-228916037  

Looks like a Clang bug to me:  
  
``` c++  
template <typename InputIt, typename T>  
constexpr T accumulate(InputIt first, InputIt last, T init) {  
    for (; first != last; ++first)  
        init = init + *first;  
    return init;  
}  
  
template <typename ...>  
struct fail {  
    static constexpr int lengths[] = {1, 2};  
    static constexpr auto sum = ::accumulate(lengths, lengths + 2, 0);  
};  
  
template struct fail<>;  
```  
  
**Edit**: This does not use Hana at all, yet it still fails and we're not violating anything `constexpr` AFAICT.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2016-06-28 00:38:43 UTC  
> Url: https://github.com/boostorg/hana/issues/292#issuecomment-228916948  

I narrowed the problem down to the following simple use case:  
  
``` cpp  
template <typename = void>  
struct foo {  
  static constexpr int arr[] = {0, 1, 2};  
  static constexpr int first = arr[0];  
};  
  
int main() {  
  constexpr int x = foo<>::first;  
}  
```  
  
Any attempt to dereference or access the element of a `constexpr` array that is declared in a class template will explode. Arrays declared in a function or top level scope works though.  
  
I made a patch that fixes `hana::flatten`, but `make check` revealed more of the same error in other parts of the code. I would gladly patch all of them if you are comfortable with it. (It would be a silver bullet in `detail::array`)  
  
Let me know if you think this is a compiler bug.  
  
PS: I think some of the implementation detail code is overly generic. `detail::accumulate` is used only once to find a sum and it uses two overloads and creates a function with `hana::_ + hana::_` and then calls the second overload which is probably not very efficient. (I changed this in my patch)

---

## Comment 3

> Username: ldionne  
> Created at: 2016-06-28 00:41:46 UTC  
> Url: https://github.com/boostorg/hana/issues/292#issuecomment-228917383  

Here's what I reduced the code to on my side:  
  
``` c++  
constexpr int dereference(int const* p) {  
    return *p;  
}  
  
template <typename ...>  
struct fail {  
    static constexpr int ints[] = {0};  
    static_assert(dereference(&ints[0]) == 0, "");  
};  
  
template struct fail<>;  
```  
  
This is definitely a bug, and I encourage you to submit a bug report.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-06-28 00:45:26 UTC  
> Url: https://github.com/boostorg/hana/issues/292#issuecomment-228917907  

Regarding the fix, I'd rather see this fixed in Clang instead of working around it in Hana. That being said, if Clang 3.9 is released with this bug, then I might apply the patch. And regarding the generality of implementation detail code, I do agree with you that it's probably overkill, but this is probably not significant in terms of compile-time performance, so I'd rather leave it as is. However, if you can show me a benchmark (for example `flatten`) where a less generic variant of the algorithms would provide better compile-time performance, then I'll happily accept your changes or make them myself.

---

## Comment 5

> Username: ldionne  
> Created at: 2016-06-29 01:19:46 UTC  
> Url: https://github.com/boostorg/hana/issues/292#issuecomment-229230775  

Closing as not a bug in Hana. Still, @ricejasonf, can you explain your workaround for `flatten` so that we can use it later on if needed?

---

## Comment 6

> Username: ricejasonf  
> Created at: 2016-07-06 03:56:27 UTC  
> Url: https://github.com/boostorg/hana/issues/292#issuecomment-230665316  

The bug only occurs when the array is declared at class template scope, so you can simply declare it inside of a static function instead.  
  
I'm currently running my stuff off of the following branch that contains the workarounds:  
  
https://github.com/ricejasonf/hana/commit/02f5bf24b1f4ae900322df8c827c0e0ddda4c6ae

---

## Comment 7

> Username: ghost  
> Created at: 2017-09-08 15:18:12 UTC  
> Updated at: 2017-09-08 15:32:34 UTC  
> Url: https://github.com/boostorg/hana/issues/292#issuecomment-328132617  

Hi guys, just ran into this clang bug with clang 5.0.0 (Boost 1.65).  
I'll change my code, but that was to tell you that it's still there one year later. Seems like it's persistent.

---

## Comment 8

> Username: ricejasonf  
> Created at: 2017-09-08 16:11:27 UTC  
> Url: https://github.com/boostorg/hana/issues/292#issuecomment-328147151  

Louis has a fix in the develop branch. https://github.com/boostorg/hana/commit/4407ab7829bc3e61238df711411afceb30db3012

---

## Comment 9

> Username: ghost  
> Created at: 2017-09-13 10:37:36 UTC  
> Updated at: 2017-09-13 10:38:23 UTC  
> Url: https://github.com/boostorg/hana/issues/292#issuecomment-329128573  

I don't want to bother, but I tried develop branch with clang 5.0 and even 6.0 and your example still can't compile (same error).  
I checked if I did download the fix and the answer is yes. So I'm the only one with the problem?

---

## Comment 10

> Username: ricejasonf  
> Created at: 2017-09-13 16:46:16 UTC  
> Url: https://github.com/boostorg/hana/issues/292#issuecomment-329227899  

The actual bug is in clang and has not been fixed. See https://bugs.llvm.org/show_bug.cgi?id=28337.  
  
We implemented the workaround, but for now it is only in the `develop` branch of Boost.Hana.  
  
The aforementioned commit has the workarounds.
