# #76 - A limitation in c++17 mode [Open]

> Username: namark  
> Created at: 2021-04-19 16:55:32 UTC  
> Updated at: 2021-04-19 16:55:32 UTC  
> Url: https://github.com/boostorg/pfr/issues/76  

Hi, I thought of using the magic of this library as a shortcut for providing a standard "tuple-like" interface for aggregates, for use in generic code that expects it, in the following way  
```c++  
#include <boost/pfr.hpp>  
#include <iostream>  
#include <tuple>  
  
namespace ma  
{  
  
	struct A  
	{  
		int a, b;  
	};  
	using boost::pfr::get;  
  
}  
  
template <> struct std::tuple_size<ma::A> :  
	boost::pfr::tuple_size<ma::A> {};  
template <std::size_t I> struct std::tuple_element<I, ma::A> :  
	boost::pfr::tuple_element<I, ma::A> {};  
  
int main()  
{  
	ma::A x{1,2};  
	using std::get;  
	std::cout << get<1>(x) << '\n';  
}  
  
```  
but in c++17 mode this creates a sort of a circular dependency. As far as I understand, the pfr::get (along with pfr::tuple_element) depends on structured binding, which (since std::tuple_size is specialized) depends on ADLed get, which is the pfr::get again. The whole thing manifests as a compiler error like this on gcc 10.2  
```  
In file included from ./pfr/include/boost/pfr.hpp:12,  
                 from boost_pfr.cpp:1:  
./pfr/include/boost/pfr/core.hpp: In instantiation of ‘constexpr decltype(auto) boost::pfr::get(T&) [with long unsigned int I = 0; T = ma::A]’:  
./pfr/include/boost/pfr/detail/core17_generated.hpp:51:10:   required from ‘constexpr auto boost::pfr::detail::tie_as_tuple(T&, boost::pfr::detail::size_t_<2>) [with T = ma::A; boost::pfr::detail::size_t_<2> = std::integral_constant<long unsigned int, 2>]’  
./pfr/include/boost/pfr/detail/core17.hpp:55:42:   required from ‘constexpr auto boost::pfr::detail::tie_as_tuple(T&) [with T = ma::A]’  
./pfr/include/boost/pfr/core.hpp:81:108:   required by substitution of ‘template<long unsigned int I, class T> using tuple_element = boost::pfr::detail::sequence_tuple::tuple_element<I, decltype (boost::pfr::detail::tie_as_tuple(declval<T&>()))> [with long unsigned int I = i; T = ma::A]’  
boost_pfr.cpp:19:36:   required from here  
./pfr/include/boost/pfr/core.hpp:54:64: error: use of ‘constexpr auto boost::pfr::detail::tie_as_tuple(T&) [with T = ma::A]’ before deduction of ‘auto’  
   54 |     return detail::sequence_tuple::get<I>( detail::tie_as_tuple(val) );  
      |                                            ~~~~~~~~~~~~~~~~~~~~^~~~~  
In file included from ./pfr/include/boost/pfr/detail/core17.hpp:10,  
                 from ./pfr/include/boost/pfr/detail/core.hpp:17,  
                 from ./pfr/include/boost/pfr/core.hpp:12,  
                 from ./pfr/include/boost/pfr.hpp:12,  
                 from boost_pfr.cpp:1:  
./pfr/include/boost/pfr/detail/core17_generated.hpp: In instantiation of ‘constexpr auto boost::pfr::detail::tie_as_tuple(T&, boost::pfr::detail::size_t_<2>) [with T = ma::A; boost::pfr::detail::size_t_<2> = std::integral_constant<long unsigned int, 2>]’:  
./pfr/include/boost/pfr/detail/core17.hpp:55:42:   required from ‘constexpr auto boost::pfr::detail::tie_as_tuple(T&) [with T = ma::A]’  
./pfr/include/boost/pfr/core.hpp:81:108:   required by substitution of ‘template<long unsigned int I, class T> using tuple_element = boost::pfr::detail::sequence_tuple::tuple_element<I, decltype (boost::pfr::detail::tie_as_tuple(declval<T&>()))> [with long unsigned int I = i; T = ma::A]’  
boost_pfr.cpp:19:36:   required from here  
./pfr/include/boost/pfr/detail/core17_generated.hpp:51:10: note: in initialization of structured binding variable ‘a’  
   51 |   auto& [a,b] = val; // ====================> Boost.PFR: User-provided type is not a SimpleAggregate.  
      |          ^  
```  
  
I guess this kind of usage is somewhat out of scope of the library, but it seems to work fine with BOOST_PFR_USE_CPP17 defined as 0.
