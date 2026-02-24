# #34 - variable 'a' is uninitialized when passed as a const reference argument here warning when compiling with clang-cl.exe [Open]

> Username: k15tfu  
> Created at: 2021-11-22 16:42:26 UTC  
> Updated at: 2022-06-14 10:55:52 UTC  
> Url: https://github.com/boostorg/concept_check/issues/34  

Hi!  
  
In file boost/concept_check.hpp:  
```  
boost/concept_check.hpp(139,38): error : variable 'a' is uninitialized when passed as a const reference argument here [-Werror,-Wuninitialized-const-reference]  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2022-04-24 02:21:31 UTC  
> Url: https://github.com/boostorg/concept_check/issues/34#issuecomment-1107688265  

@k15tfu can you provide the calling code?  Is this still an issue for you?

---

## Comment 2

> Username: k15tfu  
> Created at: 2022-06-14 10:55:52 UTC  
> Url: https://github.com/boostorg/concept_check/issues/34#issuecomment-1155028307  

Hi! Sorry for the late response, yes this is still relevant. Here is the full error message, please tell me if that's not enough:  
```  
1>In file included from my.cpp:3:  
1>In file included from boost/algorithm/string.hpp:19:  
1>In file included from boost/algorithm/string/trim.hpp:19:  
1>In file included from boost/range/as_literal.hpp:18:  
1>In file included from boost/range/iterator_range.hpp:13:  
1>In file included from boost/range/iterator_range_core.hpp:38:  
1>In file included from boost/range/functions.hpp:20:  
1>In file included from boost/range/size.hpp:21:  
1>In file included from boost/range/size_type.hpp:20:  
1>In file included from boost/range/concepts.hpp:19:  
1>boost/concept_check.hpp(139,38): error : variable 'a' is uninitialized when passed as a const reference argument here [-Werror,-Wuninitialized-const-reference]  
1>boost/concept/usage.hpp(20,43): message : in instantiation of member function 'boost::DefaultConstructible<unsigned long long>::~DefaultConstructible' requested here  
1>boost/concept/detail/general.hpp(50,42): message : in instantiation of member function 'boost::concepts::usage_requirements<boost::DefaultConstructible<unsigned long long>>::~usage_requirements' requested here  
1>boost/concept_check.hpp(137,5): message : in instantiation of member function 'boost::concepts::requirement<boost::concepts::failed ************boost::concepts::usage_requirements<boost::DefaultConstructible<unsigned long long>>::************>::failed' requested here  
1>boost/concept/usage.hpp(36,7): message : expanded from macro 'BOOST_CONCEPT_USAGE'  
1>boost/concept/assert.hpp(43,5): message : expanded from macro 'BOOST_CONCEPT_ASSERT'  
1>boost/concept/detail/general.hpp(92,51): message : expanded from macro 'BOOST_CONCEPT_ASSERT_FN'  
1>boost/icl/interval_map.hpp(126,11): message : in instantiation of member function 'boost::icl::interval_base_map<boost::icl::interval_map<unsigned long long, std::multimap<unsigned long long, my struct>, boost::icl::partial_absorber, std::less, my boost::icl::identity_based_inplace_combine, icl::inter_section, boost::icl::discrete_interval<unsigned long long>>, unsigned long long, std::multimap<unsigned long long, my struct>, boost::icl::partial_absorber, less, my boost::icl::identity_based_inplace_combine, inter_section, boost::icl::discrete_interval<unsigned long long>>::interval_base_map' requested here  
1>my.hpp(82,34): message : in instantiation of member function 'boost::icl::interval_map<unsigned long long, std::multimap<unsigned long long, my struct>, boost::icl::partial_absorber, std::less, my boost::icl::identity_based_inplace_combine, icl::inter_section, boost::icl::discrete_interval<unsigned long long>>::interval_map' requested here```
