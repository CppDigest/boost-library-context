# #15 - MSVC 2012 build error [Closed]

> Username: jeking3  
> Created at: 2018-05-21 00:25:57 UTC  
> Updated at: 2018-10-16 11:35:52 UTC  
> Closed at: 2018-10-16 00:25:33 UTC  
> Url: https://github.com/boostorg/assign/issues/15  

Admittedly this could just be a bad compiler, but this fails during CI testing.  
https://ci.appveyor.com/project/jeking3/assign/build/1.0.2-develop/job/2j92ev1wnrpe677d#L1780  
```  
libs\assign\test\multi_index_container.cpp(123) : error C2440: 'initializing' : cannot convert from 'boost::assign_detail::generic_list<employee>' to 'boost::multi_index::multi_index_container<employee,boost::multi_index::indexed_by<boost::multi_index::ordered_unique<boost::multi_index::identity<employee>,boost::mpl::na,boost::mpl::na>,boost::multi_index::ordered_non_unique<boost::multi_index::tag<name,by_name,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na>,boost::multi_index::member<employee,std::string,pointer-to-member(0x4)>,boost::mpl::na>,boost::multi_index::ordered_non_unique<boost::multi_index::tag<age,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na>,boost::multi_index::member<employee,int,pointer-to-member(0x1c)>,boost::mpl::na>,boost::multi_index::sequenced<boost::multi_index::tag<as_inserted,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na>>,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na>,std::allocator<Value>>'  
        with  
        [  
            Value=employee  
        ]  
        No constructor could take the source type, or constructor overload resolution was ambiguous  
libs\assign\test\multi_index_container.cpp(138) : error C2440: 'initializing' : cannot convert from 'boost::assign_detail::generic_list<std::string>' to 'boost::multi_index::multi_index_container<std::string,boost::multi_index::indexed_by<boost::multi_index::sequenced<boost::multi_index::tag<boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na>>,boost::multi_index::ordered_non_unique<boost::multi_index::identity<std::string>,boost::mpl::na,boost::mpl::na>,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na,boost::mpl::na>,std::allocator<_Ty>>'  
        with  
        [  
            _Ty=std::string  
        ]  
        No constructor could take the source type, or constructor overload resolution was ambiguous  
    call "bin.v2\standalone\msvc\msvc-12.0\msvc-setup.bat" x86 >nul  
 cl /Zm800 -nologo @"bin.v2\libs\assign\test\multi_index_container.test\msvc-12.0\release\cxxstd-03-iso\threading-multi\multi_index_container.obj.rsp"   
...failed compile-c-c++ bin.v2\libs\assign\test\multi_index_container.test\msvc-12.0\release\cxxstd-03-iso\threading-multi\multi_index_container.obj...  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-10-16 00:25:33 UTC  
> Updated at: 2018-10-16 11:35:52 UTC  
> Url: https://github.com/boostorg/assign/issues/15#issuecomment-430059002  

This issue is no longer seen in builds.  It appears to have been resolved by #6.
