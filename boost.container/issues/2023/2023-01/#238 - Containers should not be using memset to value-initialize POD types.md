# #238 - Containers should not be using memset to value-initialize POD types [Closed]

> Username: dangelog  
> Created at: 2023-01-27 02:29:10 UTC  
> Updated at: 2023-02-14 15:04:00 UTC  
> Closed at: 2023-02-13 07:54:04 UTC  
> Url: https://github.com/boostorg/container/issues/238  

(Apologies if this is the Nth bug report on the matter, I couldn't find any other.)  
  
Boost.Containers use `memset(0)` to value-initialize certain types, as documented [here](https://www.boost.org/doc/libs/1_81_0/doc/html/container/cpp_conformance.html#container.cpp_conformance.non_standard_memset_initialization). Users have certain macro-based opt-outs in case they don't want this to happen, although the defaults are sane (integers and floating points are zero-initialized via zero-filling on any common implementation).  
  
The problem is that this detection and zero-filling is extended to POD types by default:  
  
>  If neither `BOOST_CONTAINER_MEMZEROED_FLOATING_POINT_IS_NOT_ZERO` nor `BOOST_CONTAINER_MEMZEROED_POINTER_IS_NOT_ZERO` is defined Boost.Container also considers POD types to be value initializable via `std::memset` with value zero.   
  
This is wrong.  
  
Consider a type like `struct S { int S::*pmd; };` , this type is a POD, but zero-filling would not necessarily give a result equivalent to value initialization. On Itanium, pointers to data members are zero-initialized to -1u ([source](https://itanium-cxx-abi.github.io/cxx-abi/abi.html#data-member-pointers)). Zero-filling instead results in code such as  
  
```  
   boost::container::vector<S> v(10);  
```  
  
not correctly value-initializing its contents on Itanium. A complete testcase is here: https://gcc.godbolt.org/z/Ehdefvdro  
  
Unfortunately there's no solution / type trait in the Standard Library actually usable to perform the optimization you want to do here. You need ABI-specific codepaths for where this is safe / allowed, and workarounds for [broken compilers](https://developercommunity.visualstudio.com/t/Pointer-to-data-member-is-not-initialize/10238905). There's some discussion in this draft proposal https://github.com/ecatmur/constexpr-bit-cast-nullptr/blob/main/paper.md (many thanks to Ed Catmur).  
  
In Qt we ended up with an ad-hoc detection (no opt-outs or opt-ins), `std::is_scalar_v<T> && !std::is_member_object_pointer_v<T>;`, cf. https://github.com/qt/qtbase/commit/e8f5f20319ce5f75180c531b6e26e648ce55619c

---

## Comment 1

> Username: dangelog  
> Created at: 2023-02-13 12:33:43 UTC  
> Url: https://github.com/boostorg/container/issues/238#issuecomment-1427867653  

For the record: this was part of some research sponsored by my employer, stemming from the bug(s) in Qt that I've mentioned.  
  
The ultimate goal is to propose to add a type trait to the Standard Library, so that Boost.Container and others can use such a trait avoid ad-hoc detections.  
  
The latest draft at the time of this writing is here https://isocpp.org/files/papers/P2782R0.html , once mailed you'll find the most up-to-date version here https://wg21.link/P2782

---

## Comment 2

> Username: igaztanaga  
> Created at: 2023-02-13 14:09:58 UTC  
> Url: https://github.com/boostorg/container/issues/238#issuecomment-1428002105  

Thanks for the info. If possible, I would thank if R1 can say Boost.Container bug was fixed for Boost 1.82 ;-)

---

## Comment 3

> Username: dangelog  
> Created at: 2023-02-14 15:04:00 UTC  
> Url: https://github.com/boostorg/container/issues/238#issuecomment-1429891003  

Already done! :-)
