# #171 - -Wconversion warning with is_signed for enums [Open]

> Username: pdimov  
> Created at: 2022-07-03 17:39:12 UTC  
> Updated at: 2025-09-05 23:41:04 UTC  
> Url: https://github.com/boostorg/type_traits/issues/171  

```  
..\../boost/type_traits/is_signed.hpp: In instantiation of 'const no_cv_t boost::detail::is_signed_values<test::enum1>::minus_one':  
..\../boost/type_traits/is_signed.hpp:45:4:   required from 'const bool boost::detail::is_signed_helper<test::enum1>::value'  
..\../boost/type_traits/is_signed.hpp:74:4:   required from 'const bool boost::detail::is_signed_impl<test::enum1>::value'  
..\../boost/type_traits/is_signed.hpp:79:27:   required from 'struct boost::is_signed<test::enum1>'  
..\../boost/container_hash/hash.hpp:178:135:   required from 'typename boost::enable_if_<boost::is_enum<T>::value, long unsigned int>::type boost::hash_value(T) [with T = test::enum1; typename boost::enable_if_<boost::is_enum<T>::value, long unsigned int>::type = long unsigned int]'  
..\../boost/container_hash/hash.hpp:672:30:   required from 'std::size_t boost::hash<T>::operator()(const T&) const [with T = test::enum1; std::size_t = long unsigned int]'  
test\hash_enum_test.cpp:39:5:   required from here  
..\../boost/type_traits/is_signed.hpp:37:59: error: the result of the conversion is unspecified because '-1' is outside the range of type 'boost::detail::is_signed_values<test::enum1>::no_cv_t' {aka 'test::enum1'} [-Werror=conversion]  
   37 |    static const no_cv_t minus_one = (static_cast<no_cv_t>(-1));  
      |                                                           ^~  
```  
The warning is correct, because -1 is not necessarily a valid value for enumeration types, so this is not required to work.  
  
I'm not sure how we can fix this. I tried a few things, but nothing worked.  
  
is_signed is not particularly useful for enums, and in fact `std::is_signed` doesn't support them, so this may seem of low priority; but what I'm actually trying to implement is `underlying_type`, and its C++03 implementation is  
```  
conditional<is_signed<T>, make_signed<T>, make_unsigned<T>>::type::type  
```  
Maybe what we actually need is just `boost::underlying_type`. (Or `boost::underlying_type_`, because Core has already - and incorrectly - claimed `boost::underlying_type` in `boost/core/scoped_enum.hpp`.)

---

## Comment 1

> Username: Lastique  
> Created at: 2022-09-10 00:04:40 UTC  
> Url: https://github.com/boostorg/type_traits/issues/171#issuecomment-1242566469  

`boost::underlying_type` in `boost/core/underlying_type.hpp` does support scoped enums from `boost/core/scoped_enum.hpp` but it also supports native enums if `std::underlying_type` is available. So, if you're going to implement a generic C++03 version of `underlying_type` it should go under the name of `boost::underlying_type`. No need to add another type trait that does the same thing.

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created at: 2025-09-05 22:46:42 UTC  
> Url: https://github.com/boostorg/type_traits/issues/171#issuecomment-3259952657  

FYI, this is now a strong error with clang 21. And it breaks boost::lexical_cast with enums. See https://github.com/boostorg/lexical_cast/pull/87

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created at: 2025-09-05 23:41:04 UTC  
> Url: https://github.com/boostorg/type_traits/issues/171#issuecomment-3260063398  

On my local case where I am having trouble with `boost::lexical_cast` and an enum, this local diff https://github.com/boostorg/type_traits/pull/199 is enough to make the build work with clang 21
