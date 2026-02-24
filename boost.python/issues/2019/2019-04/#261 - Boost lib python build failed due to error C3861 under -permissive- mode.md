# #261 - Boost lib python build failed due to error C3861 under /permissive- mode [Closed]

> Username: QuellaZhang  
> Created at: 2019-04-04 03:05:19 UTC  
> Updated at: 2019-04-11 06:38:00 UTC  
> Closed at: 2019-04-11 06:38:00 UTC  
> Url: https://github.com/boostorg/python/issues/261  

**Environment:**  
VS 2017 + Windows Server 2016  
  
**Issue description:**  
We build and run test for boost and we found one test Boost\Python failed to run under /permissive- mode, and **this test only failed on x64, passed on x86**. This issue can be reproduced from boost version 497e40d. Could you please take a look?  
  
**Reproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. set _CL_=/DNOMINMAX /wd4643 /permissive-  
4. .\bootstrap  
5. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
7. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\python\test  
  
**ErrorMessage:**  
andreas_beyer.cpp  
.\boost/python/detail/unwind_type.hpp(37): **error C3861: 'unwind_type**': identifier not found  
.\boost/python/detail/unwind_type.hpp(37): note: 'unwind_type': function was not declared in the template definition context and can be found only via argument-dependent lookup in the instantiation context  
.\boost/python/detail/unwind_type.hpp(59): note: see reference to function template instantiation 'boost::python::converter::detail::unwind_type_id_helper::result_type boost::python::detail::unwind_type_cv<Generator,const U>(const U *,boost::python::detail::const_,Generator *)' being compiled  
        with  
        [  
            Generator=boost::python::converter::detail::unwind_type_id_helper,  
            U=int  
        ]  
.\boost/python/detail/unwind_type.hpp(127): note: see reference to function template instantiation 'boost::python::converter::detail::unwind_type_id_helper::result_type boost::python::detail::unwind_ptr_type<Generator,U>(U *,Generator *)' being compiled  
        with  
        [  
            Generator=boost::python::converter::detail::unwind_type_id_helper,  
            U=int  
        ]  
.\boost/python/detail/unwind_type.hpp(165): note: see reference to function template instantiation 'boost::python::converter::detail::unwind_type_id_helper::result_type boost::python::detail::unwind_helper2<2>::execute<Generator,const D>(U &(__cdecl *)(void),Generator *)' being compiled  
        with  
        [  
            Generator=boost::python::converter::detail::unwind_type_id_helper,  
            D=int,  
            U=int  
        ]  
.\boost/python/converter/pytype_function.hpp(46): note: see reference to function template instantiation 'boost::python::converter::detail::unwind_type_id_helper::result_type boost::python::detail::unwind_type<boost::python::converter::detail::unwind_type_id_helper,T>(boost::type<T> *,Generator *)' being compiled  
        with  
        [  
            T=const int &,  
            Generator=boost::python::converter::detail::unwind_type_id_helper  
        ]  
.\boost/python/converter/pytype_function.hpp(68): note: see reference to function template instantiation 'boost::python::type_info boost::python::converter::detail::unwind_type_id_<T>(boost::type<T> *,boost::mpl::false_ *)' being compiled  
        with  
        [  
            T=const int &  
        ]  
.\boost/python/converter/pytype_function.hpp(66): note: while compiling class template member function 'const PyTypeObject *boost::python::converter::expected_pytype_for_arg<const int &>::get_pytype(void)'  
.\boost/preprocessor/iteration/detail/local.hpp(40): note: see reference to function template instantiation 'const PyTypeObject *boost::python::converter::expected_pytype_for_arg<const int &>::get_pytype(void)' being compiled  
.\boost/preprocessor/iteration/detail/local.hpp(40): note: see reference to class template instantiation 'boost::python::converter::expected_pytype_for_arg<const int &>' being compiled  
.\boost/python/detail/signature.hpp(76): note: while compiling class template member function 'const boost::python::detail::signature_element *boost::python::detail::signature_arity<2>::impl<Sig>::elements(void)'  
        with  
        [  
            Sig=boost::mpl::vector3<void,A &,const int &>  
        ]  
.\boost/python/detail/caller.hpp(230): note: see reference to function template instantiation 'const boost::python::detail::signature_element *boost::python::detail::signature_arity<2>::impl<Sig>::elements(void)' being compiled  
        with  
        [  
            Sig=boost::mpl::vector3<void,A &,const int &>  
        ]  
.\boost/python/detail/signature.hpp(58): note: see reference to class template instantiation 'boost::python::detail::signature_arity<2>::impl<Sig>' being compiled  
        with  
        [  
            Sig=boost::mpl::vector3<void,A &,const int &>  
        ]

---

## Comment 1

> Username: QuellaZhang  
> Created at: 2019-04-11 06:38:00 UTC  
> Url: https://github.com/boostorg/python/issues/261#issuecomment-481985806  

This issue has been fixed on 0d0cd71, so closed this issue.
