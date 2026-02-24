# #258 - Feature request: Make `static_vector` "trivially relocatable" when possible [Open]

> Username: Quuxplusone  
> Created at: 2023-11-14 21:30:59 UTC  
> Updated at: 2024-01-03 19:27:41 UTC  
> Url: https://github.com/boostorg/container/issues/258  

It was recently discussed on [the cpplang Slack](https://cpplang.slack.com/archives/C2PQKRWJU/p1699903321902899) that Boost implements a lot of class types that are "Platonically trivially relocatable" without being _known to the compiler_ to be trivially relocatable.  
In [P1144R9](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2023/p1144r9.html#design-goals) my go-to example was `boost::movelib::unique_ptr<int>`; but in the above discussion it was pointed out that `boost::container::static_vector<int,10>` would be a less archaic example, so P1144R10 might switch to that.  
  
Executive summary: Wouldn't it be cool if we could write—  
```  
static_assert(__is_trivially_relocatable(boost::container::static_vector<int,10>));  
static_assert(!__is_trivially_relocatable(boost::container::static_vector<std::list<int>,10>));  
```  
The `__is_trivially_relocatable` is available only on Clang so far, and the necessary `std::is_trivially_relocatable_v`/`[[trivially_relocatable]]` trait/attribute are available only in [my fork of Clang/libc++](https://godbolt.org/z/ExaKc7q35); but both levels of support are detectable via the preprocessor ([see Godbolt](https://godbolt.org/z/ExaKc7q35)).  
  
I propose to add an `#ifdef` so that `static_vector` and perhaps other containers will be recognized as trivially relocatable whenever the compiler/STL support that notion.

---

## Comment 1

> Username: psiha  
> Created at: 2023-12-21 12:38:24 UTC  
> Url: https://github.com/boostorg/container/issues/258#issuecomment-1866177355  

upvote

---

## Comment 2

> Username: psiha  
> Created at: 2024-01-03 19:05:18 UTC  
> Url: https://github.com/boostorg/container/issues/258#issuecomment-1875833148  

Actually aren't all Boost.Containers ('platonically') trivially relocatable (simply by the fact of not being self-referential)?  
  
static_vector should in addition be fully trivial for all trivial Ts - yet it fails for example the std::is_trivially_move_constructible check for a trivial T.

---

## Comment 3

> Username: Quuxplusone  
> Created at: 2024-01-03 19:27:40 UTC  
> Url: https://github.com/boostorg/container/issues/258#issuecomment-1875859561  

> Actually aren't all Boost.Containers ('platonically') trivially relocatable (simply by the fact of not being self-referential)?  
  
Yes, _if_ their components are. For example,  
```  
  template<class T>  
  using ShmAlloc = boost::interprocess::allocator<T, boost::interprocess::managed_shared_memory::segment_manager>;  
  static_assert(!is_trivially_relocatable_v<boost::container::vector<int, ShmAlloc<int>>>);  
```  
because such a vector's move-construct-and-destroy needs more than `memcpy` — it needs to update the object representation of its `interprocess::offset_ptr` data member. So it's generally tricky to figure that out without a lot more machinery (one piece of which, ideally, would be STL support for `std::is_trivially_relocatable` — that's P1144).  
  
Also, I'm taking your word for it that "all Boost.Containers" are "not self-referential." E.g. I know Microsoft's `std::list<int>` is trivially relocatable because it uses a "sentinel node" on the heap; libstdc++'s and libc++'s `std::list<int>` are non-trivially relocatable because they use a "dummy node" inside the `list` object itself; but I have no idea what `boost::container::list` does. Basically `static_vector` is the only one where I'm confident I understand (and can correctly implement) all of the icky innards involved with its relocatability.  
  
I also agree that `is_trivially_move_constructible_v<T>` ought to imply `is_trivially_move_constructible_v<static_vector<T,10>>`, and `is_trivially_copyable_v<T>` ought to imply `is_trivially_copyable_v<static_vector<T,10>>`. But the latter (changing trivial copyability of a type) is an ABI break, and I don't know Boost's policies on ABI breaks. Adding the `[[trivially_relocatable]]` attribute definitely isn't an ABI break.
