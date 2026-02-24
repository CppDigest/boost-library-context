# #209 - Question about build error in capture.cpp [Open]

> Username: BinCaoWR  
> Created at: 2019-07-26 03:08:28 UTC  
> Updated at: 2019-07-26 03:08:28 UTC  
> Url: https://github.com/boostorg/hof/issues/209  

I got the following build error on PPC, gcc-8.1.0, c++14.  
Looks like only boost::hof::capture_forward(one)(binary_class())(two) and boost::hof::capture_forward(1)(binary_class())(2) report error.   
boost::hof::capture_forward(one, two)(binary_class())() and boost::hof::capture_forward()(binary_class())(one, two) have no problem.  
boost::hof::capture and boost::hof::capture_basic have no problem.  
Any suggestion what is going on?  
  
gcc.compile.c++ ../bin.v2/libs/hof/test/capture.test/gcc-8.1.0/debug/cross-compile-vxworks/static-only-on/visibility-hidden/capture.o  
In file included from /boost_1_70_0/boost/hof/detail/callable_base.hpp:11,  
                 from /boost_1_70_0/boost/hof/capture.hpp:11,  
                 from ../libs/hof/test/capture.cpp:7:  
/boost_1_70_0/boost/hof/alias.hpp: In instantiation of 'constexpr boost::hof::alias<T, Tag>::alias(FitXs&& ...) [with FitXs = {const boost::hof::alias_inherit<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, boost::hof::detail::pair_tag<1, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, binary_class> >&}; <template-parameter-2-2> = void; T = const int&; Tag = boost::hof::detail::pack_tag<boost::hof::detail::seq<0>, const int&>]':  
type_traits:1411:20:   required from 'const bool std::_Is_ntc<true, boost::hof::alias<const int&, boost::hof::detail::pack_tag<boost::hof::detail::seq<0>, const int&> >, const boost::hof::alias_inherit<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, boost::hof::detail::pair_tag<1, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, binary_class> >&>::value'  
/boost_1_70_0/boost/hof/pack.hpp:251:15:   required from 'constexpr boost::hof::detail::pack_base<boost::hof::detail::seq<Xs ...>, Ts ...>::pack_base(Xs&& ...) [with Xs = {const boost::hof::alias_inherit<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, boost::hof::detail::pair_tag<1, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, binary_class> >&}; <template-parameter-2-2> = void; long unsigned int ...Ns = {0}; Ts = {const int&}]'  
type_traits:1043:15:   required by substitution of 'template<class _Uty> static decltype (((void)((_Uty)(declval<const boost::hof::alias_inherit<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, boost::hof::detail::pair_tag<1, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, binary_class> >&>())), std::true_type())) std::_Is_constructible<true, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, const boost::hof::alias_inherit<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, boost::hof::detail::pair_tag<1, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, binary_class> >&>::_Fn<_Uty>(int) [with _Uty = boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>]'  
type_traits:1050:27:   required from 'struct std::_Is_constructible<true, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, const boost::hof::alias_inherit<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, boost::hof::detail::pair_tag<1, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, binary_class> >&>'  
type_traits:1055:9:   required from 'struct std::is_constructible<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, const boost::hof::alias_inherit<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, boost::hof::detail::pair_tag<1, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, binary_class> >&>'  
/boost_1_70_0/boost/hof/alias.hpp:125:5:   required by substitution of 'template<class ... FitXs, class> constexpr boost::hof::alias_inherit<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, boost::hof::detail::pair_tag<1, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, binary_class> >::alias_inherit(FitXs&& ...) [with FitXs = {const boost::hof::alias_inherit<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, boost::hof::detail::pair_tag<1, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, binary_class> >&}; <template-parameter-1-2> = <missing>]'  
/boost_1_70_0/boost/hof/capture.hpp:152:42:   required by substitution of 'template<class F> constexpr decltype (boost::hof::detail::capture_invoke<F, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&> >(static_cast<F&&>(f), (boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>&&)(static_cast<const boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>&>((* boost::hof::always(declval<boost::hof::detail::capture_pack<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&> >::fit_const_this_type>())(f)))))) boost::hof::detail::capture_pack<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&> >::operator()<F>(F) const [with F = binary_class]'  
../libs/hof/test/capture.cpp:28:5:   required from here  
/boost_1_70_0/boost/hof/detail/delegate.hpp:62:46: error: invalid initialization of reference of type 'const int&' from expression of type 'const boost::hof::alias_inherit<boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, boost::hof::detail::pair_tag<1, boost::hof::detail::pack_base<boost::hof::detail::seq<0>, const int&>, binary_class> >'  
     : var(BOOST_HOF_FORWARD(FitXs)(fit_xs)...) {}  
                                              ^  
/boost_1_70_0/boost/hof/detail/delegate.hpp:66:51: note: in expansion of macro 'BOOST_HOF_DELGATE_PRIMITIVE_CONSTRUCTOR'  
 #define BOOST_HOF_DELEGATE_CONSTRUCTOR(C, T, var) BOOST_HOF_DELGATE_PRIMITIVE_CONSTRUCTOR(constexpr, C, T, var)  
                                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/boost_1_70_0/boost/hof/alias.hpp:104:5: note: in expansion of macro 'BOOST_HOF_DELEGATE_CONSTRUCTOR'  
     BOOST_HOF_DELEGATE_CONSTRUCTOR(alias, T, value)  
     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
