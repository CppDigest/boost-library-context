# #52 - Boost\libs\numeric\odeint failed to build due to error C2955 and error C3203 on MSVC [Closed]

> Username: Zhaojun-Liu  
> Created at: 2022-06-07 06:23:28 UTC  
> Updated at: 2023-12-18 09:37:49 UTC  
> Closed at: 2023-12-18 09:37:48 UTC  
> Url: https://github.com/boostorg/odeint/issues/52  

**Issue Description:**  
Boost\libs\numeric\odeint failed to build due to error C2955 and error C3203 on MSVC. Could you please take a look? The master branch commit we used is a1552ab.  
  
**Build Steps:**  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git boost  
2. open a VS 2019 x86 command prompt and browse to boost  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
5. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 -j16 variant=release --build-dir=..\out\x86rel libs\numeric\odeint\test  
  
**Error Info:**  
runge_kutta_error_concepts.cpp  
.\boost/bind.hpp(41): note: The practice of declaring the Bind placeholders (_1, _2, ...) in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.  
.\boost/numeric/odeint/algebra/detail/extract_value_type.hpp(47): **error C2955**: 'boost::type': use of class template requires template argument list  
.\boost/type.hpp(14): note: see declaration of 'boost::type'  
libs\numeric\odeint\test\runge_kutta_error_concepts.cpp(150): note: see reference to class template instantiation 'boost::numeric::odeint::detail::extract_value_type<State,void>' being compiled  
        with  
        [  
            State=boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50,int32_t,void>,boost::multiprecision::et_on>  
        ]  
.\boost/mpl/aux_/has_type.hpp(20): note: see reference to class template instantiation 'error_stepper_methods<U1>' being compiled  
        with  
        [  
            U1=boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50,int32_t,void>,boost::multiprecision::et_on>  
        ]  
.\boost/mpl/aux_/has_type.hpp(20): note: while compiling class template member function 'boost::mpl::aux::yes_tag boost::mpl::aux::has_type<error_stepper_methods<U1>,boost::mpl::bool_<true>>::gcc_3_2_wknd::test(volatile const boost::mpl::aux::type_wrapper<U> *,boost::mpl::aux::type_wrapper<U::type> *)'  
        with  
        [  
            U1=boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50,int32_t,void>,boost::multiprecision::et_on>  
        ]  
.\boost/mpl/aux_/preprocessed/plain/quote.hpp(37): note: see reference to class template instantiation 'boost::mpl::aux::has_type<error_stepper_methods<U1>,boost::mpl::bool_<true>>' being compiled  
        with  
        [  
            U1=boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50,int32_t,void>,boost::multiprecision::et_on>  
        ]  
.\boost/mpl/aux_/preprocessed/plain/apply_wrap.hpp(39): note: see reference to class template instantiation 'boost::mpl::quote1<F,Tag>::apply<T1>' being compiled  
        with  
        [  
            F=error_stepper_methods,  
            Tag=boost::mpl::void_,  
            T1=boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50,int32_t,void>,boost::multiprecision::et_on>  
        ]  
...........  
libs\numeric\odeint\test\runge_kutta_error_concepts.cpp(171): note: see reference to class template instantiation 'boost::mpl::copy<container_types,boost::mpl::inserter<boost::mpl::vector0<boost::mpl::na>,boost::mpl::insert_range<boost::mpl::_1,boost::mpl::end<boost::mpl::_1>,error_stepper_methods<boost::mpl::_2>>>>' being compiled  
libs\numeric\odeint\test\runge_kutta_error_concepts.cpp(151): **error C3203**: 'type': unspecialized class template can't be used as a template argument for template parameter 'Value', expected a real type  
libs\numeric\odeint\test\runge_kutta_error_concepts.cpp(152): error C3203: 'type': unspecialized class template can't be used as a template argument for template parameter 'Value', expected a real type  
libs\numeric\odeint\test\runge_kutta_error_concepts.cpp(153): error C3203: 'type': unspecialized class template can't be used as a template argument for template parameter 'Value', expected a real type  
libs\numeric\odeint\test\runge_kutta_error_concepts.cpp(154): error C3203: 'type': unspecialized class template can't be used as a template argument for template parameter 'Value', expected a real type  
  
**Detailed log file:**  
[test.log.7.log](https://github.com/boostorg/odeint/files/8850598/test.log.7.log)

---

## Comment 1

> Username: mborland  
> Created at: 2023-12-18 09:37:48 UTC  
> Url: https://github.com/boostorg/odeint/issues/52#issuecomment-1859942794  

Fixed by: https://github.com/boostorg/odeint/pull/63
