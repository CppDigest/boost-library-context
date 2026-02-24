# #160 Add preliminary support for generic serialization [Open]

> Username: sdebionne  
> Created at: 2019-06-04 08:26:32 UTC  
> Updated at: 2019-07-08 12:39:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/160  

See #113 for the rationale.  
  
One possible use case is generic support for Boost.Hana Structs that have some level of reflection:  
  
```c++  
#include <type_traits>  
  
#include <boost/hana/fwd/define_struct.hpp>  
#include <boost/hana/fwd/for_each.hpp>  
#include <boost/hana/fwd/second.hpp>  
  
namespace boost {  
    namespace serialization {  
  
/// Template specialization for Boost.Hana structures  
template <typename Archive, typename T>  
std::enable_if_t<hana::Struct<T>::value> serialize(Archive& ar, T& d, const unsigned int version)  
{  
    hana::for_each(d, [&ar](auto member) { ar & hana::second(member); });  
}  
  
    } // namespace serialization  
} // namespace boost  
```  
  
Let me know if there is a better way to properly test for std=c++11 and the availability of <type_traits>.  
  
Is there any other place in the library where functionalities are conditionally supported given a c++ standard level?

---

## Comment 1

> Username: sdebionne  
> Created_at: 2019-06-14 09:15:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/160#issuecomment-502035207  

@HDembinski @robertramey Any chance that you guys find time to review this PR? If accepted, it would  be great to have in 1.71...

---

## Review 2 [Commented]

> Username: HDembinski  
> Created_at: 2019-06-14 10:55:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/160#pullrequestreview-249837589  

📁 include/boost/serialization/serialization.hpp

```diff
  77 |+         -> typename
  78 |+         std::is_same<
  79 |+             decltype(std::declval<T>().serialize(std::declval<Archive&>(), std::declval<unsigned int>())),
```

> Username: HDembinski  
> Created_at: 2019-06-14 10:55:20 UTC  
> Updated_at: 2019-07-08 12:39:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/160#discussion_r293759288  

Where is Archive& coming from here?


---

## Review 3 [Commented]

> Username: HDembinski  
> Created_at: 2019-06-14 10:59:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/160#pullrequestreview-249838028  

📁 include/boost/serialization/serialization.hpp

```diff
  65 | BOOST_STRONG_TYPEDEF(unsigned int, version_type)
  66 | 
  67 |+ #if __cplusplus==201103L
```

> Username: HDembinski  
> Created_at: 2019-06-14 10:56:30 UTC  
> Updated_at: 2019-07-08 12:39:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/160#discussion_r293759615  

Use detection from boost.config instead of raw checks like this.

---

📁 include/boost/serialization/serialization.hpp

```diff
  14 | #include <boost/serialization/strong_typedef.hpp>
  15 | 
  16 |+ #if !defined(BOOST_NO_CXX11_HDR_TYPE_TRAITS)
```

> Username: HDembinski  
> Created_at: 2019-06-14 10:57:31 UTC  
> Updated_at: 2019-07-08 12:39:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/160#discussion_r293759903  

You don't need <type_traits>, versions of enable_if and is_same are in boost.core.

---

📁 include/boost/serialization/serialization.hpp

```diff
  84 |+     static constexpr std::false_type check(...);
  85 |+ 
  86 |+     typedef decltype(check<C>(0)) type;
```

> Username: HDembinski  
> Created_at: 2019-06-14 10:58:41 UTC  
> Updated_at: 2019-07-08 12:39:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/160#discussion_r293760256  

Perhaps Boost.Concept should be used instead of writing this manually.


---

## Comment 4

> Username: HDembinski  
> Created_at: 2019-06-14 10:59:32 UTC  
> Url: https://github.com/boostorg/serialization/pull/160#issuecomment-502065939  

I am not the maintainer of this library, but here is a review.

---

## Comment 5

> Username: HDembinski  
> Created_at: 2019-06-14 11:02:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/160#issuecomment-502066598  

The whole check can be implemented in pre-C++11 code, I think. https://stackoverflow.com/questions/257288/is-it-possible-to-write-a-template-to-check-for-a-functions-existence

---

## Comment 6

> Username: robertramey  
> Created_at: 2019-06-14 15:33:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/160#issuecomment-502155761  

> Let me know if there is a better way to properly test for std=c++11 and the availability of <type_traits>.  
  
Take serious look at Boost config.  It has been the cornerstone of boost serialization portability.  People take boost serialization portability for granted, but it's in fact an incredible feat which is totally due to the efforts of John Maddock's Boost config.  I never test for any specific C++ version - only the existence of a capability as specified by boost config.  It has to be this way because compilers which purport to support C++11 are not all in sync which combination of C++11 features they support.  The fact is that there is no defacto C++ standard - only a theoretical one.  
  
> Is there any other place in the library where functionalities are conditionally supported given a c++ standard level?  
  
easy. look through the bjam test files and the tests themselves.

---

## Comment 7

> Username: HDembinski  
> Created_at: 2019-06-14 18:28:52 UTC  
> Url: https://github.com/boostorg/serialization/pull/160#issuecomment-502216335  

@robertramey I share your admiration of boost.config :)

---
