# #77 Member pointers functionality [Open]

> Username: denzor200  
> Created at: 2021-04-25 20:11:44 UTC  
> Updated at: 2025-03-24 17:37:25 UTC  
> Url: https://github.com/boostorg/pfr/pull/77  

Hello Anton. I have some idea and i want to suggest it for you. This patch will add pretty useful functionality for extracting member pointer of some structure. For example:  
```  
struct Foo  
{  
	char ch;  
	short id;  
	short opt;  
	int value;  
};  
  
auto ch_memptr = boost::pfr::get_memptr<0>(Foo{});  
auto id_memptr = boost::pfr::get_memptr<1>(Foo{});  
auto opt_memptr = boost::pfr::get_memptr<2>(Foo{});  
auto value_memptr = boost::pfr::get_memptr<3>(Foo{});  
  
auto obj = Foo{};  
  
obj.*ch_memptr = 'c';  
obj.*id_memptr = 100;  
obj.*opt_memptr = 200;  
obj.*value_memptr = 3000;  
  
std::cout << obj.ch << " " << obj.id << " " << obj.opt << " " << obj.value << std::endl; ///< will print "c 100 200 3000"  
```  
Its just a draft - not a final code. If you are interested - i will continue for it.

---

## Comment 1

> Username: denzor200  
> Created_at: 2021-04-25 20:14:43 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#issuecomment-826383245  

This may help to resolve this issue:  
https://github.com/boostorg/pfr/issues/62  
BUT  i unfortunately do not know how to make `get_memptr` constexpr((

---

## Comment 2

> Username: apolukhin  
> Created_at: 2021-05-06 16:08:13 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#issuecomment-833646045  

Good idea!  
  
I''ve simplified it a little bit https://godbolt.org/z/e8rKWrrEs  
  
In my implementation problems are the same:  
* pointer arithmetic is not a constant expression (bit_cast refuses to convert pointers to integers)   
* Something must be done with the default constructor

---

## Comment 3

> Username: denzor200  
> Created_at: 2021-06-17 18:21:24 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#issuecomment-863460955  

> Something must be done with the default constructor  
  
1. `template<typename T>  
struct declval_helper  
{  
    static T value;  
};`  
But it not for clang and msvc  
https://godbolt.org/z/nYz6M61YT  
2. What if we pass a NULL reference into boost::pfr::get? This is works good, but i cant anticipate the consequences

---

## Comment 4

> Username: denzor200  
> Created_at: 2021-06-19 12:59:17 UTC  
> Updated_at: 2021-06-19 13:12:22 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#issuecomment-864403171  

> Something must be done with the default constructor  
3. Why don't we use the already checked `offset_based_getter`? In my opinion it solves this problem good.  
https://godbolt.org/z/hfxbnvqrE  
This works on MSVC too

---

## Comment 5

> Username: schaumb  
> Created_at: 2022-02-23 21:07:21 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#issuecomment-1049216790  

I tried to construct constexpr member pointer getter:  
  
GCC and msvc are promising:   
https://godbolt.org/z/hPa9718aY  
https://godbolt.org/z/Th8KTKxns  
  
Caveats:  
- msvc static assert: same member pointer at runtime, but not recognized as equal at compile time.  
- only for not final classes   
- clang not works with this technique

---

## Comment 6

> Username: denzor200  
> Created_at: 2022-02-23 22:34:00 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#issuecomment-1049281322  

@schaumb interesting. Unfortunatelly, `refl` cannot be passed as template parameter, only `orig` can.

---

## Comment 7

> Username: schaumb  
> Created_at: 2022-02-24 17:41:36 UTC  
> Updated_at: 2022-02-24 17:41:54 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#issuecomment-1050099616  

@denzor200   
  
I created a [gcc ticket](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=104678), because it is a compiler bug (probably?).  
  
msvc constexpr member pointers can be used as template parameter. But:  
```  
template<auto> struct X {};  
static_assert(std::is_same_v<X<refl>, X<orig>>);  // ERROR  
  
  
template <auto V> decltype(V) wash() { return V; };  
static_assert(&wash<refl> == &wash<orig>);  // OK  
static_assert(wash<refl>() == wash<orig>()); // OK  
// compiler save first call function instance template argument (currently the refl), so:  
static_assert(wash<refl>() == refl); // OK  
static_assert(wash<orig>() == orig); // ERROR  
```  
  
Not equality problem occures only if the original member pointer is used. But `wash` can be used as equality checker.

---

## Comment 8

> Username: denzor200  
> Created_at: 2022-07-15 17:33:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#issuecomment-1185756592  

@schaumb  
I created clang ticket here: https://github.com/llvm/llvm-project/issues/56541  
  
Let's see where the answer will be faster :)

---

## Review 9 [Changes requested]

> Username: apolukhin  
> Created_at: 2023-05-21 07:18:31 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/77#pullrequestreview-1435483765  

Looks good! Here's a few nitpicks

📁 include/boost/pfr/core_memptr.hpp

```diff
  39 |+ /// \endcode
  40 |+ template<std::size_t I, class T>
  41 |+ inline auto get_memptr(boost::pfr::type_identity<T>) noexcept
```

> Username: apolukhin  
> Created_at: 2023-05-21 07:11:44 UTC  
> Updated_at: 2023-05-21 07:18:31 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1199710031  

Let's change it to   
```  
template<std::size_t I, class T>  
inline auto get_memptr() noexcept  
```  
  
In that case the type_identity is not required and should be removed. The   
```  
template<std::size_t I, class T>  
inline auto get_memptr(const T&) noexcept  
```  
overload is not required either


📁 include/boost/pfr/detail/core_memptr.hpp

```diff
  69 |+     std::size_t space = sizeof(T);
  70 |+ 
  71 |+     return tie_as_offsets_tuple_impl<T>(detail::make_index_sequence<detail::fields_count<T>()>{}, offset, space);
```

> Username: apolukhin  
> Created_at: 2023-05-21 07:13:35 UTC  
> Updated_at: 2023-05-21 07:18:31 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1199710227  

add a namespace to `tie_as_offsets_tuple_impl` to avoid ADL

---

📁 include/boost/pfr/detail/core_memptr.hpp

```diff
  88 |+     );
  89 |+ 
  90 |+     return tie_as_memptrs_tuple_impl<T>(detail::make_index_sequence<detail::fields_count<T>()>{});
```

> Username: apolukhin  
> Created_at: 2023-05-21 07:14:01 UTC  
> Updated_at: 2023-05-21 07:18:31 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1199710270  

add a namespace to `tie_as_memptrs_tuple_impl` to avoid ADL


📁 include/boost/pfr/detail/memptr_cast.hpp

```diff
  33 |+         );
  34 |+ 
  35 |+         union {
```

> Username: apolukhin  
> Created_at: 2023-05-21 07:15:41 UTC  
> Updated_at: 2023-05-21 07:18:31 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1199710481  

That's an UB. Just use std::memcpy. The efficiency would remain the same - compilers know how to optimize std::memcpy


📁 include/boost/pfr/detail/align.hpp

```diff
  17 |+         //BOOST_ASSERT(boost::alignment::detail::is_alignment(alignment)); TODO enable
  18 |+         if (size <= space) {
  19 |+             std::size_t p = ~(alignment - 1) & (offset + alignment - 1);
```

> Username: apolukhin  
> Created_at: 2023-05-21 07:16:28 UTC  
> Updated_at: 2023-05-21 07:18:31 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1199710610  

provide a more clear names for local variables instead of `p` and `n`


📁 test/run/memptr.cpp

```diff
  13 |+     short opt;
  14 |+     int value;
  15 |+ };
```

> Username: apolukhin  
> Created_at: 2023-05-21 07:17:26 UTC  
> Updated_at: 2023-05-21 07:18:31 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1199710736  

add a non-trivial type to an aggregate, `std:;string` for example


---

## Review 10 [Commented]

> Username: denzor200  
> Created_at: 2023-08-13 00:02:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/77#pullrequestreview-1575515109  

📁 test/run/memptr.cpp

```diff
   1 |+ // Copyright (c) 2021 Denis Mikhailov
```

> Username: denzor200  
> Created_at: 2023-08-13 00:01:55 UTC  
> Updated_at: 2023-08-13 00:02:03 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1292558366  

Move this file to the `test/core_memptr` dir

> Username: denzor200  
> Created_at: 2023-11-18 16:02:21 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1398231295  

All new test filed should be moved to `test/core_memptr`


---

## Review 11 [Commented]

> Username: denzor200  
> Created_at: 2023-11-18 16:01:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/77#pullrequestreview-1738428464  

📁 test/core/compile-fail/packed.cpp

```diff
  16 |+ 
  17 |+ int main() {
  18 |+ 	(void)boost::pfr::get_memptr<3>(boost::pfr::type_identity<Foo>{}); // Must be a compile time error
```

> Username: denzor200  
> Created_at: 2023-11-18 15:55:14 UTC  
> Updated_at: 2023-11-18 16:01:37 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1398230373  

tabs

---

📁 test/core/compile-fail/packed.cpp

```diff
   7 |+ 
   8 |+ #pragma pack(1)
   9 |+ struct Foo
```

> Username: denzor200  
> Created_at: 2023-11-18 16:01:19 UTC  
> Updated_at: 2023-11-18 16:01:37 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1398231142  

Provide the same test but with a structure like this:  
```  
#pragma pack(1)  
struct sample  
{  
    int ch;  
    int id;  
    int opt;  
    int value;  
};  
```  
In order to test the case when settled alignment doesn't affect the size.  
  
BTW default align is not always `sizeof(int)`. You should provide some logic for select appropriate type between `int`, `long`, etc and for do nothing when nothing was selected for this platform.


---

## Review 12 [Commented]

> Username: denzor200  
> Created_at: 2025-03-02 20:11:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/77#pullrequestreview-2652805776  

📁 include/boost/pfr/detail/memptr_cast.hpp

```diff
  35 |+         union {
  36 |+             T memptr;
  37 |+             raw_type offset_;
```

> Username: denzor200  
> Created_at: 2025-03-02 20:08:03 UTC  
> Updated_at: 2025-03-02 20:11:08 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1976705359  

`raw_type` must always be `ptrdiff_t` due to Itanium C++ ABI's limitations. See https://itanium-cxx-abi.github.io/cxx-abi/abi.html#member-pointers

> Username: denzor200  
> Created_at: 2025-03-24 17:37:25 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r2010650383  

don't forget to check if `sizeof(ptrdiff_t) == sizeof(int M::*)` in order to catch possible strange compilers configuration

---

📁 include/boost/pfr/detail/memptr_cast.hpp

```diff
  17 |+     constexpr std::uint16_t unsigned_by_size(size_t_<2>) noexcept { return 0; }
  18 |+     constexpr std::uint32_t unsigned_by_size(size_t_<4>) noexcept { return 0; }
  19 |+     constexpr std::uint64_t unsigned_by_size(size_t_<8>) noexcept { return 0; }
```

> Username: denzor200  
> Created_at: 2025-03-02 20:10:59 UTC  
> Updated_at: 2025-03-02 20:11:08 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1976706018  

It's incorrect to use an unsigned type to represent a member pointer, see neighbour thread. Here must be a bug, provide unit-test to reproduce it.


---

## Comment 13

> Username: denzor200  
> Created_at: 2025-03-02 20:43:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#issuecomment-2692898629  

> * Something must be done with the default constructor  
  
Look at this: https://godbolt.org/z/T1dvvzhT6  
I've just remembered about `boost::pfr::detail::fake_object`:)

---

## Comment 14

> Username: jcelerier  
> Created_at: 2025-03-02 20:44:53 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#issuecomment-2692899234  

this feature would be incredibly useful for my use-cases! I had to ressort to ultra-gnarly hacks so far

---

## Review 15 [Commented]

> Username: denzor200  
> Created_at: 2025-03-02 21:29:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/77#pullrequestreview-2652823724  

📁 include/boost/pfr/detail/core_memptr.hpp

```diff
  86 |+         !std::is_union<T>::value,
  87 |+         "====================> Boost.PFR: For safety reasons it is forbidden to reflect unions. See `Reflection of unions` section in the docs for more info."
  88 |+     );
```

> Username: denzor200  
> Created_at: 2025-03-02 21:28:47 UTC  
> Updated_at: 2025-03-02 21:29:05 UTC  
> Url: https://github.com/boostorg/pfr/pull/77#discussion_r1976722156  

make sure that `T` doesn't have virtual functions and multiple inheritance. Looks at the sample to understand the problem: https://godbolt.org/z/5zarzPPGP


---
