# #37 - Compile error, VS 2017 Boost 1.69 property_tree equal_range [Open]

> Username: BobNagy  
> Created at: 2019-01-19 17:53:23 UTC  
> Updated at: 2019-01-19 21:38:41 UTC  
> Url: https://github.com/boostorg/property_tree/issues/37  

I am using VS 2017 15.9.5, Boost 1.69.0.  
  
Code that compiles and works correctly using VS 2015 now causes a compile error with VS 2017.  
Unable to find resolution or workaround.  
  
The error is this :  
  
boost\property_tree\detail\ptree_implementation.hpp(500): error C2440: '<function-style-cast>': cannot convert from '_Ty1' to 'boost::property_tree::basic_ptree<std::wstring,std::wstring,std::less<Key>>::assoc_iterator'  
        with  
        [  
            _Ty1=boost::multi_index::detail::bidir_node_iterator<boost::multi_index::detail::ordered_index_node<boost::multi_index::detail::null_augment_policy,boost::multi_index::detail::index_node_base<std::pair<const std::wstring,boost::property_tree::basic_ptree<std::wstring,std::wstring,std::less<std::wstring>>>,std::allocator<std::pair<const std::wstring,boost::property_tree::basic_ptree<std::wstring,std::wstring,std::less<std::wstring>>>>>>>  
        ]  
        and  
        [  
            Key=std::wstring  
        ]  
boost\property_tree\detail\ptree_implementation.hpp(500): note: No constructor could take the source type, or constructor overload resolution was ambiguous (compiling source file BPropertyTree.cpp)

---

## Comment 1

> Username: BobNagy  
> Created at: 2019-01-19 21:38:41 UTC  
> Url: https://github.com/boostorg/property_tree/issues/37#issuecomment-455817456  

Also, this error only happens if the call to equal_range is inside a lambda.  
If the call is inside a member function, there is no error.
