# #147 - bitwise operations between different enumeration types are deprecated [Closed]

> Username: jefftrull  
> Created at: 2022-01-27 07:02:43 UTC  
> Updated at: 2022-02-05 23:44:58 UTC  
> Closed at: 2022-02-05 23:44:58 UTC  
> Url: https://github.com/boostorg/wave/issues/147  

Starting in C++20, various kinds of arithmetic operations are deprecated as described in [P1120R0](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p1120r0.html). Among them, apparently, are the bitwise operations Wave uses to identify token categories - operations between `token_id` and `token_category`, specifically. There are many ([here](https://github.com/boostorg/wave/blob/59610d6f79951fae841b9403fbdf5dfd63b45867/include/boost/wave/util/cpp_iterator.hpp#L471) is one example) but it may be possible to solve it in a small number of places with an appropriate cast.

---

## Comment 1

> Username: hkaiser  
> Created at: 2022-01-27 10:29:54 UTC  
> Url: https://github.com/boostorg/wave/issues/147#issuecomment-1023064117  

Alternatively, we could define the operators: https://eel.is/c++draft/bitmask.types

---

## Comment 2

> Username: jefftrull  
> Created at: 2022-02-05 23:44:58 UTC  
> Url: https://github.com/boostorg/wave/issues/147#issuecomment-1030715233  

closed by #148
