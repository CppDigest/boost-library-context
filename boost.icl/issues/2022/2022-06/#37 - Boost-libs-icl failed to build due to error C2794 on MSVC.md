# #37 - [MSVC][std:c++latest] Boost\libs\icl failed to build due to error C2794 on MSVC [Open]

> Username: QuellaZhang  
> Created at: 2022-06-27 08:44:49 UTC  
> Updated at: 2022-06-27 08:44:49 UTC  
> Url: https://github.com/boostorg/icl/issues/37  

Issue description:  
Boost\libs\icl failed to build due to error C2794 on MSVC with /std:c++latest. Could you please take a look? We ues boostorg/boost@0724ce0 from Boost master branch.  
  
**Build step:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git boost  
2. open a VS 2019 x86 command prompt and browse to boost  
3. set _CL_=/std:c++latest  
4. .\bootstrap  
5. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=86  
6. .\b2 variant=release --build-dir=..\out\x86rel address-model=86  
7. .\b2 -j16 variant=release --build-dir=..\out\x86rel libs\icl\test  
  
**Error info:**  
test_misc.cpp  
C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.29.30133\include\__msvc_iter_core.hpp(166): error C2794: 'value_type': is not a member of any direct or indirect base class of 'std::indirectly_readable_traits<boost::icl::element_iterator<std::_Tree_iterator<std::_Tree_val<std::_Tree_simple_types<std::pair<const boost::icl::discrete_interval<DomainT,Compare>,int>>>>>>'  
        with  
        [  
            DomainT=T,  
            Compare=std::less  
        ]  
C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.29.30133\include\xutility(809): note: see reference to alias template instantiation 'std::iter_value_t<_InIt>' being compiled  
        with  
        [  
            _InIt=boost::icl::element_iterator<std::_Tree_iterator<std::_Tree_val<std::_Tree_simple_types<std::pair<const boost::icl::discrete_interval<T,std::less>,int>>>>>  
        ]  
C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.29.30133\include\xutility(4735): note: see reference to alias template instantiation 'std::_Iter_value_t<_InIt>' being compiled  
        with  
        [  
            _InIt=boost::icl::element_iterator<std::_Tree_iterator<std::_Tree_val<std::_Tree_simple_types<std::pair<const boost::icl::discrete_interval<T,std::less>,int>>>>>  
        ]  
C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.29.30133\include\xutility(4793): note: see reference to function template instantiation '_InIt std::_Find_unchecked<_InIt,_Ty>(_InIt,const _InIt,const _Ty &)' being compiled  
        with  
        [  
            _InIt=boost::icl::element_iterator<std::_Tree_iterator<std::_Tree_val<std::_Tree_simple_types<std::pair<const boost::icl::discrete_interval<T,std::less>,int>>>>>,  
            _Ty=std::pair<const int,int>  
        ]  
libs\icl\test\test_misc_\test_misc.cpp(108): note: see reference to function template instantiation '_InIt std::find<boost::icl::element_iterator<std::_Tree_iterator<std::_Tree_val<std::_Tree_simple_types<std::pair<const boost::icl::discrete_interval<DomainT,Compare>,int>>>>>,std::pair<const int,int>>(_InIt,const _InIt,const _Ty &)' being compiled  
        with  
        [  
            _InIt=boost::icl::element_iterator<std::_Tree_iterator<std::_Tree_val<std::_Tree_simple_types<std::pair<const boost::icl::discrete_interval<T,std::less>,int>>>>>,  
            DomainT=T,  
            Compare=std::less,  
            _Ty=std::pair<const int,int>  
        ]
