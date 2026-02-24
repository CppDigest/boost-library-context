# #73 - Compile error in include/boost/stl_interfaces/iterator_interface.hpp when compiling with /std:c++latest [Closed]

> Username: NEIL-smtg  
> Created at: 2024-12-18 05:38:18 UTC  
> Updated at: 2025-01-31 08:56:12 UTC  
> Closed at: 2025-01-31 08:55:55 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/73  

Hi, I work on Microsoft Visual C++ testing, where we regularly build popular open-source projects, including Boost, with development builds of our compiler and libraries with some particular cl options such as /permissive- and /std:c++latest to detect and prevent shipping regressions that would affect you.  
  
This issue is only occurring when building with `/std:c++latest`, if there are no plans to support `/std:c++latest` it in the future, feel free to close this issue.  
  
Recently, as I build Boost from source and compiling with /std:c++latest, I encounter the following errors in https://github.com/boostorg/stl_interfaces/blob/develop/include/boost/stl_interfaces/iterator_interface.hpp:  
```  
.\boost/stl_interfaces/iterator_interface.hpp(657): error C2139: 'iterator<node>': an undefined class is not allowed as an argument to compiler intrinsic type trait '__is_base_of'  
libs\stl_interfaces\test\compile_sfinae_path_mutable_iterator.cpp(19): note: see declaration of 'iterator<node>'  
.\boost/stl_interfaces/iterator_interface.hpp(657): note: the template instantiation context (the oldest one first) is  
libs\stl_interfaces\test\compile_sfinae_path_mutable_iterator.cpp(73): note: see reference to class template instantiation 'iterator<node>' being compiled  
libs\stl_interfaces\test\compile_sfinae_path_mutable_iterator.cpp(57): note: see reference to class template instantiation 'boost::stl_interfaces::v3::iterator_interface<iterator<node>,std::forward_iterator_tag,Node,ValueType *,ptrdiff_t>' being compiled  
        with  
        [  
            Node=node,  
            ValueType=std::forward_iterator_tag  
        ]  
.\boost/stl_interfaces/iterator_interface.hpp(682): note: see reference to function template instantiation 'auto boost::stl_interfaces::v2::v2_dtl::category_tag<IteratorConcept,ReferenceType>(void)' being compiled  
        with  
        [  
            IteratorConcept=iterator<node>,  
            ReferenceType=node  
        ]  
.\boost/stl_interfaces/iterator_interface.hpp(657): note: see reference to variable template 'const bool is_base_of_v<std::forward_iterator_tag,iterator<node> >' being compiled  
.\boost/stl_interfaces/iterator_interface.hpp(940): error C2976: 'boost::stl_interfaces::v2::v2_dtl::iterator_category_base': too few template arguments  
.\boost/stl_interfaces/iterator_interface.hpp(683): note: see declaration of 'boost::stl_interfaces::v2::v2_dtl::iterator_category_base'  
libs\stl_interfaces\test\compile_sfinae_path_mutable_iterator.cpp(57): error C3210: 'iterator_interface<iterator<node>,std::forward_iterator_tag,node,std::forward_iterator_tag *,__int64>': a member using-declaration can only be applied to a base class member  
```  
  
Repro Steps:  
  
1. Open x64 native tools command prompt for VS 2022  
2. git clone https://github.com/boostorg/boost.git  
3. set \_CL\_= /std:c++latest /DBOOST_TIMER_ENABLE_DEPRECATED /Zc:equalityRewrite- /Zc:enumTypes %_CL_%  
4. cd /d C:\gitP\boostorg\boost  
5. .\bootstrap 2>&1  
6. .\b2 headers variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86 2>&1  
7. .\b2 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86 2>&1  
8. set \_CL\_=%\_CL\_% /Zc:__cplusplus  
9. .\b2 -j16 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 libs\stl_interfaces\test address-model=64 2>&1  
  
Build log: [Build.log](https://github.com/user-attachments/files/18174377/Build.log)  
stl_interfaces Test log: [Test.log.94.txt](https://github.com/user-attachments/files/18174355/Test.log.94.txt)

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-12-20 03:16:33 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/73#issuecomment-2556205220  

Thanks very much for reporting this!  I'll try to produce a fix.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-12-27 04:01:43 UTC  
> Updated at: 2024-12-27 04:05:21 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/73#issuecomment-2563289631  

@NEIL-smtg So, I had a closer look at this.  It *seems* like it may be a compiler regression.  I say this because this code:  
  
https://github.com/boostorg/stl_interfaces/blob/cefbd3014ab1b0c305c4e58f650757fa4fa81040/include/boost/stl_interfaces/iterator_interface.hpp#L924-L945  
  
... is guarded by `BOOST_STL_INTERFACES_USE_DEDUCED_THIS`, as you can see at the top (`BOOST_STL_INTERFACES_DOXYGEN` is only defined in doc builds).  Also note that the first template parameter is `IteratorConcept`, *not* the derived type `D`.  Earlier versions of the template (in namespaces `v1::` and `v2::`) use CRTP, and so the derived type `D` appears as the first template parameter to those templates.  
  
Here is the code that exercises the failure:  
  
https://github.com/boostorg/stl_interfaces/blob/cefbd3014ab1b0c305c4e58f650757fa4fa81040/test/compile_sfinae_path_mutable_iterator.cpp#L18-L26  
  
That code is written to conditionally provide the derived type `D` as the first template parameter, based on the value of `BOOST_STL_INTERFACES_USE_DEDUCED_THIS` -- if it's `0`, we provide `D` first, and if it's `1`, we provide the iterator category first.  This allows this test to work with the `v1`/`v2` and `v3` template parameter expectations I explained above.  
  
The latest `vN` namespace that your compiler supports becomes `inline` -- that's why the .cpp code is using the version of `iterator_interface` from `v3::`.  
  
It looks like in the same TU, we have different definitions of `BOOST_STL_INTERFACES_USE_DEDUCED_THIS` (`1` in the `iterator_interface.hpp` code, and `0` later, in the .cpp file), unless I'm missing something.  Note that this build passes with recent releases of VS2022, which is why it seems to me to be a VS2022 dev-build regression.  
  
Let me know what you think of this analysis -- I may well be confused. :)

---

## Comment 3

> Username: NEIL-smtg  
> Created at: 2025-01-31 08:55:55 UTC  
> Updated at: 2025-01-31 08:56:12 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/73#issuecomment-2626683827  

Sorry, my mistake. I just realized this wasn't a /std:c++latest issue—it occurred because I added /Zc:__cplusplus while compiling the source files, which led to the error. I'll close this issue. Thanks!
