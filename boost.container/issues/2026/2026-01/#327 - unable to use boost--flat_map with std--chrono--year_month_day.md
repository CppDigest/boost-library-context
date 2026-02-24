# #327 - unable to use boost::flat_map with std::chrono::year_month_day [Closed]

> Username: ingo-loehken  
> Created at: 2026-01-06 19:27:21 UTC  
> Updated at: 2026-01-21 13:12:11 UTC  
> Closed at: 2026-01-21 13:12:11 UTC  
> Url: https://github.com/boostorg/container/issues/327  

maybe a combination of is_trivially_copyable & is_standard_layout can do the same job as deprecated is_pod and do not break the builds ?  
  
  
Build Output, msvc latest,  
```  
boost\1.90.0\include\boost\move\detail\type_traits.hpp(903,30): error C4647: behavior change: __is_pod(std::chrono::year_month_day) has different value in previous versions  
1>(compiling source file '/GetVersionDate.cpp')  
1>    boost\1.90.0\include\boost\move\detail\type_traits.hpp(903,30):  
1>    the template instantiation context (the oldest one first) is  
1>        GetVersionDate.cpp(44,65):  
1>        see reference to class template instantiation 'boost::container::flat_map<std::string,std::chrono::year_month_day,std::less<Key>,void>' being compiled  
1>        with  
1>        [  
1>            Key=std::string  
1>        ]  
1>        boost\1.90.0\include\boost\container\flat_map.hpp(178,16):  
1>        see reference to class template instantiation 'boost::container::dtl::flat_tree<std::pair<Key,T>,boost::container::dtl::select1st<Key>,Compare,AllocatorOrContainer>' being compiled  
1>        with  
1>        [  
1>            Key=std::string,  
1>            T=std::chrono::year_month_day,  
1>            Compare=std::less<std::string>,  
1>            AllocatorOrContainer=void  
1>        ]  
1>        boost\1.90.0\include\boost\container\detail\flat_tree.hpp(508,37):  
1>        see reference to class template instantiation 'boost::container::vector<Value,AllocatorOrVoid,void>' being compiled  
1>        with  
1>        [  
1>            Value=std::pair<std::string,std::chrono::year_month_day>,  
1>            AllocatorOrVoid=void  
1>        ]  
1>        boost\1.90.0\include\boost\container\vector.hpp(1213,5):  
1>        while compiling class template member function 'boost::container::vector<Value,AllocatorOrVoid,void>::~vector(void) noexcept'  
1>        with  
1>        [  
1>            Value=std::pair<std::string,std::chrono::year_month_day>,  
1>            AllocatorOrVoid=void  
1>        ]  
1>            boost\1.90.0\include\boost\container\detail\flat_tree.hpp(577,1):  
1>            see the first reference to 'boost::container::vector<Value,AllocatorOrVoid,void>::~vector' in 'boost::container::dtl::flat_tree<std::pair<Key,T>,boost::container::dtl::select1st<Key>,Compare,AllocatorOrContainer>::Data::~Data'  
1>        with  
1>        [  
1>            Value=std::pair<std::string,std::chrono::year_month_day>,  
1>            AllocatorOrVoid=void  
1>        ]  
1>        and  
1>        [  
1>            Key=std::string,  
1>            T=std::chrono::year_month_day,  
1>            Compare=std::less<std::string>,  
1>            AllocatorOrContainer=void  
1>        ]  
1>        boost\1.90.0\include\boost\container\vector.hpp(1215,25):  
1>        see reference to function template instantiation 'dtl::enable_if_trivially_destructible<I,void>::type boost::container::destroy_alloc_n(Allocator &,I,size_t)' being compiled  
1>        boost\1.90.0\include\boost\container\vector.hpp(1215,25):  
1>        see reference to class template instantiation 'boost::container::dtl::enable_if_trivially_destructible<T *,void>' being compiled  
1>        with  
1>        [  
1>            T=std::pair<std::string,std::chrono::year_month_day>  
1>        ]  
1>        lug.boost\1.90.0\include\boost\container\detail\copy_move_algo.hpp(320,69):  
1>        see reference to class template instantiation 'boost::move_detail::is_trivially_destructible<std::pair<Key,T>>' being compiled  
1>        with  
1>        [  
1>            Key=std::string,  
1>            T=std::chrono::year_month_day  
1>        ]  
1>        boost\1.90.0\include\boost\container\detail\pair.hpp(641,6):  
1>        see reference to class template instantiation 'boost::move_detail::is_trivially_destructible<boost::container::dtl::pair<Key,T>>' being compiled  
1>        with  
1>        [  
1>            Key=std::string,  
1>            T=std::chrono::year_month_day  
1>        ]  
1>        boost\1.90.0\include\boost\container\detail\pair.hpp(636,90):  
1>        see reference to class template instantiation 'boost::move_detail::is_trivially_destructible<B>' being compiled  
1>        with  
1>        [  
1>            B=std::chrono::year_month_day  
1>        ]  
1>        boost\1.90.0\include\boost\move\detail\type_traits.hpp(1033,30):  
1>        see reference to class template instantiation 'boost::move_detail::is_pod<T>' being compiled  
1>        with  
1>        [  
1>            T=std::chrono::year_month_day  
1>        ]  
1>        boost\1.90.0\include\boost\move\detail\type_traits.hpp(907,6):  
1>        see reference to class template instantiation 'boost::move_detail::is_pod_noextents_cv<std::chrono::year_month_day>' being compiled  
```  
  
I tried to reproduce the isseu with [godbolt](https://www.godbolt.org/z/7GPchrf69), but msvc could not find the header ?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-21 13:12:11 UTC  
> Url: https://github.com/boostorg/container/issues/327#issuecomment-3778072610  

That warning is disable for default for good reasons:  
  
https://learn.microsoft.com/en-us/cpp/preprocessor/compiler-warnings-that-are-off-by-default?view=msvc-170  
  
Note that /Wall is not recommended and even the standard C and C++ headers from Visual Studio don't support warning-free builds in /Wall.
