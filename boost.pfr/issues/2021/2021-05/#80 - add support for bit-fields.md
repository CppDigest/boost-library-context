# #80 - add support for bit-fields [Open]

> Username: bgaifullin  
> Created at: 2021-05-18 16:56:44 UTC  
> Updated at: 2025-06-20 14:38:14 UTC  
> Url: https://github.com/boostorg/pfr/issues/80  

Lets implement support for bit-fields for read-only mode.

---

## Comment 1

> Username: schaumb  
> Created at: 2022-03-09 18:10:32 UTC  
> Updated at: 2024-01-09 16:34:16 UTC  
> Url: https://github.com/boostorg/pfr/issues/80#issuecomment-1063214365  

C++14 loophole and classic methods don't work constexpr time. So I'm focusing on the C++17 solution.  
  
---  
  
I created a [branch](https://github.com/schaumb/pfr/tree/bitfield), and some changes.  
  
First, I modified the structure binding functions to return not the tuples of references, but getter and setter lambdas.  
Getter has the option to get by reference or by value. So now the types can be returned without trying to get member references.  
  
I tried to manage the `has_bitfield_member<T>` decision problem from 2 sides.  
  
Has/Is bitfield member  
---  
  
[a `can_has_a_bitfield` check](https://github.com/schaumb/pfr/blob/bitfield/include/boost/pfr/detail/core17.hpp#L133) which returns true, if the class can contain a bitfield. This is too wide currently, if a struct has any integral type, the function returns true.  
(note: bitfield can only be integral type)  
  
---  
  
[a `clearly_has_a_bitfield` check](https://github.com/schaumb/pfr/blob/bitfield/include/boost/pfr/detail/core17.hpp#L106) which returns true, if the class knowly contains a bitfield. This is not handling all the cases.  
  
Missing parts:  
- packed structures (but can be `static_assert`)  
- if size equals to `sequence_tuple::tuple<types...>` (aka the bitfields use the same size as if they weren't bitfields) :  
  - not trivial types. (This probably can be tightened for the types that contain any member that is not constexpr default initializable)  
  - contains a bool member.  
  - contains a bitfield with maximum wide, like `char c: 8;`  
  
  
Bitfield offset and size  
---  
  
A bitfield size can be calculated with [this trick](https://github.com/schaumb/pfr/blob/bitfield/include/boost/pfr/detail/core17.hpp#L50) (except for `bool`-s)  
  
For the bitfield offset I can not come up with an idea with standard C++17, constexpr time.  
  
---  
  
C++20 has more opportunity to "guess" the type layout with the [layout compatible](https://en.cppreference.com/w/cpp/types/is_layout_compatible) type trait, and the [bit cast](https://en.cppreference.com/w/cpp/numeric/bit_cast). These help for the bitfield offset, but not all the cases; only for the standard_layout types.

---

## Comment 2

> Username: schaumb  
> Created at: 2022-03-09 18:29:58 UTC  
> Updated at: 2024-01-09 16:34:54 UTC  
> Url: https://github.com/boostorg/pfr/issues/80#issuecomment-1063231553  

Probably can be managed to create not only "read-only" mode, but "proxy" mode too. So If integral types return with a proxy type like:  
```cpp  
template<class Getter, class Setter>  
struct Proxy {  
    using type = std::invoke_result_t<Getter, /*no reference*/>;  
    operator type() const {  
      return getter(/* no reference */);  
    }  
    operator type&() const {  
      return getter(/*reference*/);  
    }  
    template<class Any>  
    Proxy& operator=(Any&& val) {  
        setter(val);  
        return *this;  
    }  
private:  
    Getter getter;  
    Setter setter;  
};  
```  
This can be used for bitfield structs too.

---

## Comment 3

> Username: schaumb  
> Created at: 2024-01-09 18:42:29 UTC  
> Updated at: 2024-01-09 18:45:18 UTC  
> Url: https://github.com/boostorg/pfr/issues/80#issuecomment-1883591745  

C++20 is helping. With `consteval` and `layout_compatible` it can be guessed the bit-field members and their [sizes](https://godbolt.org/z/6ce3xoMd5).  
  
So the `clearly_has_a_bitfield check` cases can be solved.  
  
Remaining limitation:  
- if size equals to `sequence_tuple::tuple<types...>` (aka the bitfields use the same size as if they weren't bitfields) AND it contains integral type AND  
  - any member is non-copiable, non-movable and non-default-constructible  
  - if the class is not standard layout type OR the compiler does not support __cpp_lib_is_layout_compatible (clang), and:  
    - contains a bool member  
    - contains a bitfield with maximum wide, like `char c: 8;`
