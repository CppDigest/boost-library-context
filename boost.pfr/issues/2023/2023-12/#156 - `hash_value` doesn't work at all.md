# #156 - `hash_value` doesn't work at all [Open]

> Username: denzor200  
> Created at: 2023-12-26 21:28:05 UTC  
> Updated at: 2023-12-26 21:28:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/156  

https://godbolt.org/z/q6McGYzGW  
  
```  
error: no matching function for call to 'hash_detector_msvc_helper<const my_struct&, const my_struct&>(long int)'  
   55 |     template <class T1, class T2> using hash_detector = decltype(hash_detector_msvc_helper<T1,T2>(1L));  
      |                                                                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
/opt/compiler-explorer/libs/boost_1_83_0/boost/pfr/detail/detectors.hpp:53:29: note: candidate: 'template<class S> decltype (std::hash<_Tp>{}(declval<S>())) boost::pfr::detail::hash_detector_msvc_helper(long int)'  
   53 |     template <class S> auto hash_detector_msvc_helper(long) -> decltype(std::hash<S>{}(std::declval<S>()));  
      |                             ^~~~~~~~~~~~~~~~~~~~~~~~~  
/opt/compiler-explorer/libs/boost_1_83_0/boost/pfr/detail/detectors.hpp:53:29: note:   template argument deduction/substitution failed:  
/opt/compiler-explorer/libs/boost_1_83_0/boost/pfr/detail/detectors.hpp:55:98: error: wrong number of template arguments (2, should be 1)  
   55 |     template <class T1, class T2> using hash_detector = decltype(hash_detector_msvc_helper<T1,T2>(1L));  
      |                                                                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
/opt/compiler-explorer/libs/boost_1_83_0/boost/pfr/detail/detectors.hpp:53:29: note: provided for 'template<class S> decltype (std::hash<_Tp>{}(declval<S>())) boost::pfr::detail::hash_detector_msvc_helper(long int)'  
   53 |     template <class S> auto hash_detector_msvc_helper(long) -> decltype(std::hash<S>{}(std::declval<S>()));  
      |                             ^~~~~~~~~~~~~~~~~~~~~~~~~  
/opt/compiler-explorer/libs/boost_1_83_0/boost/pfr/detail/detectors.hpp:54:38: note: candidate: 'template<class S> boost::pfr::detail::can_not_apply boost::pfr::detail::hash_detector_msvc_helper(int)'  
   54 |     template <class S> can_not_apply hash_detector_msvc_helper(int);  
      |                                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/opt/compiler-explorer/libs/boost_1_83_0/boost/pfr/detail/detectors.hpp:54:38: note:   template argument deduction/substitution failed:  
/opt/compiler-explorer/libs/boost_1_83_0/boost/pfr/detail/detectors.hpp:55:98: error: wrong number of template arguments (2, should be 1)  
   55 |     template <class T1, class T2> using hash_detector = decltype(hash_detector_msvc_helper<T1,T2>(1L));  
      |                                                                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
/opt/compiler-explorer/libs/boost_1_83_0/boost/pfr/detail/detectors.hpp:54:38: note: provided for 'template<class S> boost::pfr::detail::can_not_apply boost::pfr::detail::hash_detector_msvc_helper(int)'  
   54 |     template <class S> can_not_apply hash_detector_msvc_helper(int);  
      |                                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
BTW no issues with `boost::pfr::hash_fields`
