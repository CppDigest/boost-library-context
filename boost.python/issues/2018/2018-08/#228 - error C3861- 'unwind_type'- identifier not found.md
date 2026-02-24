# #228 - error C3861: 'unwind_type': identifier not found [Closed]

> Username: bowie7070  
> Created at: 2018-08-16 04:34:44 UTC  
> Updated at: 2019-03-25 15:02:04 UTC  
> Closed at: 2019-03-25 15:02:04 UTC  
> Url: https://github.com/boostorg/python/issues/228  

Hi,  
  
I was using boost.python 1.67.0 quite happily with Visual Studio 15.7.6 and have today upgraded to Visual Studio 15.8.0 and now I get a compilation errors listed below. I am not familiar enough with the boost.python code to establish what has gone wrong so any insights would be appreciated.  
  
1>------ Build started: Project: boost-python-lnlsv, Configuration: Release Win32 ------  
1>boost_python_lnlsv.cpp  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\unwind_type.hpp(37): error C3861: 'unwind_type': identifier not found  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\unwind_type.hpp(37): note: 'unwind_type': function was not declared in the template definition context and can be found only via argument-dependent lookup in the instantiation context  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\unwind_type.hpp(59): note: see reference to function template instantiation 'boost::python::converter::detail::unwind_type_id_helper::result_type boost::python::detail::unwind_type_cv<Generator,const U>(const U *,boost::python::detail::const_,Generator *)' being compiled  
1>        with  
1>        [  
1>            Generator=boost::python::converter::detail::unwind_type_id_helper,  
1>            U=const LN_LSV::CalibrationParameters  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\unwind_type.hpp(127): note: see reference to function template instantiation 'boost::python::converter::detail::unwind_type_id_helper::result_type boost::python::detail::unwind_ptr_type<Generator,U>(U *,Generator *)' being compiled  
1>        with  
1>        [  
1>            Generator=boost::python::converter::detail::unwind_type_id_helper,  
1>            U=const LN_LSV::CalibrationParameters  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\unwind_type.hpp(165): note: see reference to function template instantiation 'boost::python::converter::detail::unwind_type_id_helper::result_type boost::python::detail::unwind_helper2<2>::execute<Generator,const LN_LSV::CalibrationParameters>(U &(__cdecl *)(void),Generator *)' being compiled  
1>        with  
1>        [  
1>            Generator=boost::python::converter::detail::unwind_type_id_helper,  
1>            U=const LN_LSV::CalibrationParameters  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\converter\pytype_function.hpp(46): note: see reference to function template instantiation 'boost::python::converter::detail::unwind_type_id_helper::result_type boost::python::detail::unwind_type<boost::python::converter::detail::unwind_type_id_helper,T>(boost::type<T> *,Generator *)' being compiled  
1>        with  
1>        [  
1>            T=const LN_LSV::CalibrationParameters &,  
1>            Generator=boost::python::converter::detail::unwind_type_id_helper  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\converter\pytype_function.hpp(68): note: see reference to function template instantiation 'boost::python::type_info boost::python::converter::detail::unwind_type_id_<T>(boost::type<T> *,boost::mpl::false_ *)' being compiled  
1>        with  
1>        [  
1>            T=const LN_LSV::CalibrationParameters &  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\converter\pytype_function.hpp(66): note: while compiling class template member function 'const PyTypeObject *boost::python::converter::expected_pytype_for_arg<const LN_LSV::CalibrationParameters &>::get_pytype(void)'  
1>c:\vcpkg\installed\x86-windows\include\boost\preprocessor\iteration\detail\local.hpp(43): note: see reference to function template instantiation 'const PyTypeObject *boost::python::converter::expected_pytype_for_arg<const LN_LSV::CalibrationParameters &>::get_pytype(void)' being compiled  
1>c:\vcpkg\installed\x86-windows\include\boost\preprocessor\iteration\detail\local.hpp(43): note: see reference to class template instantiation 'boost::python::converter::expected_pytype_for_arg<const LN_LSV::CalibrationParameters &>' being compiled  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\signature.hpp(76): note: while compiling class template member function 'const boost::python::detail::signature_element *boost::python::detail::signature_arity<3>::impl<Sig>::elements(void)'  
1>        with  
1>        [  
1>            Sig=boost::mpl::vector4<LN_LSV::LocalVolsAndLeverage,LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &>  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\caller.hpp(230): note: see reference to function template instantiation 'const boost::python::detail::signature_element *boost::python::detail::signature_arity<3>::impl<Sig>::elements(void)' being compiled  
1>        with  
1>        [  
1>            Sig=boost::mpl::vector4<LN_LSV::LocalVolsAndLeverage,LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &>  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\signature.hpp(58): note: see reference to class template instantiation 'boost::python::detail::signature_arity<3>::impl<Sig>' being compiled  
1>        with  
1>        [  
1>            Sig=boost::mpl::vector4<LN_LSV::LocalVolsAndLeverage,LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &>  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\caller.hpp(230): note: see reference to class template instantiation 'boost::python::detail::signature<Sig>' being compiled  
1>        with  
1>        [  
1>            Sig=boost::mpl::vector4<LN_LSV::LocalVolsAndLeverage,LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &>  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\caller.hpp(229): note: while compiling class template member function 'boost::python::detail::py_func_sig_info boost::python::detail::caller_arity<3>::impl<F,CallPolicies,Sig>::signature(void)'  
1>        with  
1>        [  
1>            F=LN_LSV::LocalVolsAndLeverage (__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &),  
1>            CallPolicies=boost::python::default_call_policies,  
1>            Sig=boost::mpl::vector4<LN_LSV::LocalVolsAndLeverage,LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &>  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\object\py_function.hpp(48): note: see reference to function template instantiation 'boost::python::detail::py_func_sig_info boost::python::detail::caller_arity<3>::impl<F,CallPolicies,Sig>::signature(void)' being compiled  
1>        with  
1>        [  
1>            F=LN_LSV::LocalVolsAndLeverage (__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &),  
1>            CallPolicies=boost::python::default_call_policies,  
1>            Sig=boost::mpl::vector4<LN_LSV::LocalVolsAndLeverage,LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &>  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\detail\caller.hpp(167): note: see reference to class template instantiation 'boost::python::detail::caller_arity<3>::impl<F,CallPolicies,Sig>' being compiled  
1>        with  
1>        [  
1>            F=LN_LSV::LocalVolsAndLeverage (__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &),  
1>            CallPolicies=boost::python::default_call_policies,  
1>            Sig=boost::mpl::vector4<LN_LSV::LocalVolsAndLeverage,LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &>  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\make_function.hpp(37): note: see reference to class template instantiation 'boost::python::detail::caller<F,CallPolicies,Sig>' being compiled  
1>        with  
1>        [  
1>            F=LN_LSV::LocalVolsAndLeverage (__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &),  
1>            CallPolicies=boost::python::default_call_policies,  
1>            Sig=boost::mpl::vector4<LN_LSV::LocalVolsAndLeverage,LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &>  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\make_function.hpp(104): note: see reference to function template instantiation 'boost::python::api::object boost::python::detail::make_function_aux<LN_LSV::LocalVolsAndLeverage(__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &),boost::python::default_call_policies,boost::mpl::vector4<LN_LSV::LocalVolsAndLeverage,LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &>>(F,const CallPolicies &,const Sig &)' being compiled  
1>        with  
1>        [  
1>            F=LN_LSV::LocalVolsAndLeverage (__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &),  
1>            CallPolicies=boost::python::default_call_policies,  
1>            Sig=boost::mpl::vector4<LN_LSV::LocalVolsAndLeverage,LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &>  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\def.hpp(82): note: see reference to function template instantiation 'boost::python::api::object boost::python::make_function<LN_LSV::LocalVolsAndLeverage(__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &)>(F)' being compiled  
1>        with  
1>        [  
1>            F=LN_LSV::LocalVolsAndLeverage (__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &)  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\python\def.hpp(91): note: see reference to function template instantiation 'boost::python::api::object boost::python::detail::make_function1<LN_LSV::LocalVolsAndLeverage(__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &)>(T,...)' being compiled  
1>        with  
1>        [  
1>            T=LN_LSV::LocalVolsAndLeverage (__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &)  
1>        ]  
1>c:\users\owe043\documents\svn-risklab\ln-lsv\trunk\lsv-static\boost_python_lnlsv.cpp(229): note: see reference to function template instantiation 'void boost::python::def<LN_LSV::LocalVolsAndLeverage(__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &)>(const char *,Fn)' being compiled  
1>        with  
1>        [  
1>            Fn=LN_LSV::LocalVolsAndLeverage (__cdecl *)(LN_LSV::int_days_t,const LN_LSV::MarketDataForPricing &,const LN_LSV::CalibrationParameters &)  
1>        ]  
1>c:\vcpkg\installed\x86-windows\include\boost\bind\placeholders.hpp(54): note: see reference to class template instantiation 'boost::arg<9>' being compiled  
1>c:\vcpkg\installed\x86-windows\include\boost\bind\placeholders.hpp(53): note: see reference to class template instantiation 'boost::arg<8>' being compiled  
1>c:\vcpkg\installed\x86-windows\include\boost\bind\placeholders.hpp(52): note: see reference to class template instantiation 'boost::arg<7>' being compiled  
1>c:\vcpkg\installed\x86-windows\include\boost\bind\placeholders.hpp(51): note: see reference to class template instantiation 'boost::arg<6>' being compiled  
1>c:\vcpkg\installed\x86-windows\include\boost\bind\placeholders.hpp(50): note: see reference to class template instantiation 'boost::arg<5>' being compiled  
1>c:\vcpkg\installed\x86-windows\include\boost\bind\placeholders.hpp(49): note: see reference to class template instantiation 'boost::arg<4>' being compiled  
1>c:\vcpkg\installed\x86-windows\include\boost\bind\placeholders.hpp(48): note: see reference to class template instantiation 'boost::arg<3>' being compiled  
1>c:\vcpkg\installed\x86-windows\include\boost\bind\placeholders.hpp(47): note: see reference to class template instantiation 'boost::arg<2>' being compiled  
1>c:\vcpkg\installed\x86-windows\include\boost\bind\placeholders.hpp(46): note: see reference to class template instantiation 'boost::arg<1>' being compiled  
1>Done building project "boost-python-lnlsv.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========

---

## Comment 1

> Username: colakoyeh  
> Created at: 2018-08-16 05:26:10 UTC  
> Url: https://github.com/boostorg/python/issues/228#issuecomment-413429811  

I have the same question and solved it, I use 1.66.0,  
Try this:  
  
1. open unwind_type.hpp  
2. move line 33~53 to line 94  
3. compile it!  
  
I hope this will help you.

---

## Comment 2

> Username: bowie7070  
> Created at: 2018-08-16 05:46:15 UTC  
> Url: https://github.com/boostorg/python/issues/228#issuecomment-413432844  

Thanks. I tried what you suggested and it worked. I also tried changing the use of the following in multiple locations:  
  
#ifndef _MSC_VER  
  
to:  
  
#if _MSC_VER >= 1915   
  
Since it seems, according to the comments, to be behaviour of a quite old Visual C++ that needs to be avoided.  
  
Now my problem is what to do about this longer term.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-08-16 13:03:22 UTC  
> Url: https://github.com/boostorg/python/issues/228#issuecomment-413537834  

I'd encourage you to submit a PR (ideally with some inline comments explaining the fix).

---

## Comment 4

> Username: bowie7070  
> Created at: 2018-08-16 20:15:49 UTC  
> Url: https://github.com/boostorg/python/issues/228#issuecomment-413671083  

I believe I have submitted a pull request. It has the change to the conditional compilation and a comment as to why it is necessary.   
  
https://github.com/boostorg/python/pull/229  
  
The ci checks seem to have failed but the error messages seem to be in relation to pip usage???

---

## Comment 5

> Username: brandl-muc  
> Created at: 2019-03-25 14:57:48 UTC  
> Url: https://github.com/boostorg/python/issues/228#issuecomment-476234399  

This just hit me when migrating to /permissive- so bumping this.  
@stefanseefeld The PR by @bowie7070 did not receive any attention. Anything that can be done about the pip issue they mentioned to get this merged?

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2019-03-25 15:02:04 UTC  
> Url: https://github.com/boostorg/python/issues/228#issuecomment-476236351  

Thanks for the reminder. This had slipped under my radar. I have just merged it into `develop`, and will try to get it into `master` for 1.70 if possible.
