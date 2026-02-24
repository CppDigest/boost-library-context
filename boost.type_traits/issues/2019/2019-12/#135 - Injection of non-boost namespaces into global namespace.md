# #135 - Injection of non-boost namespaces into global namespace [Open]

> Username: ned14  
> Created at: 2019-12-16 14:17:55 UTC  
> Updated at: 2019-12-16 14:17:55 UTC  
> Url: https://github.com/boostorg/type_traits/issues/135  

Like the global namespace injection in include/bind.hpp, this needs to begin being deprecated immediately and removed in a near future Boost as it causes symbol collision between incompatible Boosts:  
  
```  
boost/mpl/aux_/adl_barrier.hpp:#   define BOOST_MPL_AUX_ADL_BARRIER_NAMESPACE mpl_  
boost/mpl/aux_/adl_barrier.hpp:#   define BOOST_MPL_AUX_ADL_BARRIER_NAMESPACE_OPEN namespace mpl_ {  
boost/type_traits/integral_constant.hpp:namespace mpl_{  
boost/type_traits/integral_constant.hpp:      using ::mpl_::bool_;  
boost/type_traits/integral_constant.hpp:      using ::mpl_::integral_c;  
boost/type_traits/integral_constant.hpp:      using ::mpl_::integral_c_tag;  
```
