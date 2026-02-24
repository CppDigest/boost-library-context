# #164 - Licensing question for index_sequence.hpp [Closed]

> Username: mablanchard  
> Created at: 2021-03-25 12:55:39 UTC  
> Updated at: 2021-06-03 18:49:36 UTC  
> Closed at: 2021-06-03 18:49:36 UTC  
> Url: https://github.com/boostorg/context/issues/164  

In [`index_sequence.hpp`](https://github.com/boostorg/context/blob/develop/include/boost/context/detail/index_sequence.hpp#L32), the `index_sequence` template declaration when `BOOST_CONTEXT_NO_CXX14_INTEGER_SEQUENCE` is defined seems to be [copied from a Stack Overflow thread](https://stackoverflow.com/questions/17424477/implementation-c14-make-integer-sequence) (the SO thread is linked in the Boost source file). If I understand the [Stack Overflow licensing notice](https://stackoverflow.com/help/licensing) correctly, that code is licensed under [CC BY-SA 3.0](https://creativecommons.org/licenses/by-sa/3.0/) (snippet initially posted in 2015). I'm not a jurist (at all!) but couldn't that be incompatible with Boost.Context being licensed under Boost Software License? Not sure how it can be mitigated if it is though...

---

## Comment 1

> Username: olk  
> Created at: 2021-03-25 13:32:06 UTC  
> Url: https://github.com/boostorg/context/issues/164#issuecomment-806748952  

no idea - I'm not a lawyer  
We could imply remove the so boost.context, boost.coroutine2 and boost.fiber become C++14 libraries.

---

## Comment 2

> Username: olk  
> Created at: 2021-06-03 18:49:36 UTC  
> Url: https://github.com/boostorg/context/issues/164#issuecomment-854098128  

code removed, ty
