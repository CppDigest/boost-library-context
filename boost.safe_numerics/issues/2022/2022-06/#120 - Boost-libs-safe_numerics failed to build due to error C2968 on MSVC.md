# #120 - Boost\libs\safe_numerics failed to build due to error C2968 on MSVC [Open]

> Username: QuellaZhang  
> Created at: 2022-06-01 06:43:10 UTC  
> Updated at: 2022-06-01 06:43:10 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/120  

**Issue description:**  
Boost\libs\safe_numerics failed to build due to error C2968 on MSVC. Could you please take a look? We ues https://github.com/boostorg/boost/commit/8bfc57e268c536f68d3832741bccdefad534d9d4 from Boost master branch.  
  
**Build step:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git boost  
2. open a VS 2019 x86 command prompt and browse to boost  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=86  
5. .\b2 variant=release --build-dir=..\out\x86rel address-model=86  
6. .\b2 -j16 variant=release --build-dir=..\out\x86rel libs\safe_numerics\test  
  
**Error info:**  
test_cast.cpp  
libs\safe_numerics\test\test_cast.cpp(17): warning C4068: unknown pragma 'GCC'  
libs\safe_numerics\test\test_cast.cpp(18): warning C4068: unknown pragma 'GCC'  
libs\safe_numerics\test\test_cast.cpp(88): warning C4068: unknown pragma 'GCC'  
F:\gitP\boostorg\boost\boost\safe_numerics\safe_base_operations.hpp(371): error C2968: 'legal_overload<boost::safe_numerics::bitwise_or_operator,int,std::_Iosb<int>::_Iostate>': recursive alias declaration  
F:\gitP\boostorg\boost\boost\safe_numerics\safe_base_operations.hpp(1630): note: see reference to function template instantiation 'boost::lazy_enable_if_c<legal_overload<boost::safe_numerics::bitwise_or_operator,T,U>::value,boost::safe_numerics::bitwise_or_result<T,U>>::type boost::safe_numerics::operator |(const T &,const U &)' being compiled  
.\boost/mp11/utility.hpp(87): note: see reference to alias template instantiation 'boost::safe_numerics::bitwise_or_operator<std::ios_base::fmtflags,std::_Iosb<int>::_Iostate>' being compiled  
F:\gitP\boostorg\boost\boost\safe_numerics\safe_base_operations.hpp(364): note: see reference to class template instantiation 'boost::mp11::detail::mp_valid_impl<boost::safe_numerics::bitwise_or_operator,T,std::_Iosb<int>::_Iostate>' being compiled  
        with  
        [  
            T=std::ios_base::fmtflags  
        ]  
F:\gitP\boostorg\boost\boost\safe_numerics\safe_base_operations.hpp(366): note: see reference to alias template instantiation 'boost::mp11::mp_valid<boost::safe_numerics::bitwise_or_operator,std::ios_base::fmtflags,std::_Iosb<int>::_Iostate>' being compiled  
F:\gitP\boostorg\boost\boost\safe_numerics\safe_base_operations.hpp(1627): note: see reference to alias template instantiation 'boost::safe_numerics::`anonymous-namespace'::legal_overload<boost::safe_numerics::bitwise_or_operator,std::ios_base::iostate,std::_Iosb<int>::_Iostate>' being compiled  
F:\gitP\boostorg\boost\boost\safe_numerics\safe_base_operations.hpp(1639): note: see reference to function template instantiation 'std::enable_if<legal_overload<boost::safe_numerics::bitwise_or_operator,T,U>::value,T>::type boost::safe_numerics::operator |=(T &,const U &)' being compiled  
C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.29.30133\include\xlocnum(635): note: while compiling class template member function '_InIt std::num_get<wchar_t,_InIt>::do_get(_InIt,_InIt,std::ios_base &,std::ios_base::iostate &,void *&) const'  
        with  
        [  
            _InIt=std::istreambuf_iterator<wchar_t,std::char_traits<wchar_t>>  
        ]  
C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.29.30133\include\xlocnum(1626): note: see reference to class template instantiation 'std::num_get<wchar_t,std::istreambuf_iterator<wchar_t,std::char_traits<wchar_t>>>' being compiled
