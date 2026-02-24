# #200 - saturate_cast [Open]

> Username: gpeterhoff  
> Created at: 2025-08-23 16:33:14 UTC  
> Updated at: 2025-08-23 23:36:49 UTC  
> Url: https://github.com/boostorg/core/issues/200  

With C++26, there is std::saturate_cast https://en.cppreference.com/w/cpp/numeric/saturate_cast.html  
Can you provide this as well?  
I'm not sure if boost::core is the right library.  
My implementation requires boost::is_integer (https://github.com/boostorg/type_traits/issues/186), but they didn't understand what I meant and therefore rejected/closed it.  
  
```  
//	Distributed under the Boost Software License Version 1.0 https://www.boost.org/LICENSE_1_0.txt  
//	Copyright Gero Peterhoff  
  
#ifndef BOOST_CORE_SATURATE_CAST_HPP  
#define BOOST_CORE_SATURATE_CAST_HPP  
  
#include <boost/type_traits/is_integer.hpp>  
#include <limits>  
  
  
  
namespace boost  
{  
namespace core  
{  
template <typename To, typename From>  
inline BOOST_CXX14_CONSTEXPR typename std::enable_if<boost::is_integer<To>::value && boost::is_integer<From>::value, To>::type	saturate_cast(const From& from)	noexcept  
{  
	using limits = std::numeric_limits<To>;  
  
	BOOST_IF_CONSTEXPR (std::is_same<To, From>::value)  
	{  
		return from;  
	}  
	else BOOST_IF_CONSTEXPR (std::is_signed<To>::value && std::is_unsigned<From>::value)  
	{  
		using type = typename std::make_unsigned<To>::type;  
		return  
			(from > type(limits::max())) ? limits::max() :  
			To(from);  
	}  
	else BOOST_IF_CONSTEXPR (std::is_unsigned<To>::value && std::is_signed<From>::value)  
	{  
		using type = typename std::make_unsigned<From>::type;  
		return  
			(from < 0) ? To{} :  
			(type(from) > limits::max()) ? limits::max() :  
			To(from);  
	}  
	else BOOST_IF_CONSTEXPR (std::is_signed<To>::value && std::is_signed<From>::value)  
	{  
		return  
			(from < limits::min()) ? limits::min() :  
			(from > limits::max()) ? limits::max() :  
			To(from);  
	}  
	else  
	{  
		return  
			(from > limits::max()) ? limits::max() :  
			To(from);  
	}  
}  
  
}	//	core  
}	//	boost  
  
#endif	//	BOOST_CORE_SATURATE_CAST_HPP  
```  
  
thx  
Gero

---

## Comment 1

> Username: pdimov  
> Created at: 2025-08-23 16:56:00 UTC  
> Url: https://github.com/boostorg/core/issues/200#issuecomment-3217174249  

We tend to put these into Boost.Compat nowadays.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2025-08-23 19:09:45 UTC  
> Updated at: 2025-08-23 19:22:48 UTC  
> Url: https://github.com/boostorg/core/issues/200#issuecomment-3217264830  

Are there any guidelines/documents that could be followed?   
There are, for example,  
core  
compat  
compatibility  
...  
Which one is correct?  
There are also some similar implementations, e.g. boost::core::detail::string_view and boost::utility::string_view.  
Can you please consolidate this?   
I can't see through this thicket anymore.  
  
In addition, some libraries are “stand alone,” meaning they implement *everything* themselves instead of using available libraries. This is not only cumbersome but also prone to errors.  
  
Years ago, there was a paper (I think it was even written by you, but I can't find it right now) that said that only C++ code from the last 10 years should be supported, and everything older than that should be discarded. Then boost could go from being a conservative library back to a progressive one.

---

## Comment 3

> Username: pdimov  
> Created at: 2025-08-23 22:59:32 UTC  
> Url: https://github.com/boostorg/core/issues/200#issuecomment-3217463057  

Compat is correct. We used Core before Compat was added. Core is also still C++03, which needlessly complicates implementations.

---

## Comment 4

> Username: Lastique  
> Created at: 2025-08-23 23:36:49 UTC  
> Url: https://github.com/boostorg/core/issues/200#issuecomment-3217478679  

> Core is also still C++03, which needlessly complicates implementations.  
  
This is not accurate. Each component in Core has its own C++ version requirement. New additions need not be compatible with C++03.
