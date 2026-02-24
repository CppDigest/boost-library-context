# #30 - operators.hpp(154,1): fatal error C1001: Internal compiler error [Open]

> Username: CoeurAJoie  
> Created at: 2021-05-07 11:34:05 UTC  
> Updated at: 2024-03-12 06:25:56 UTC  
> Url: https://github.com/boostorg/bimap/issues/30  

Hi  
  
I am migrating to VS2019, boost Release 1.70.0  
C++ Language Standard ISO C++ 14  
C Language Standard: Default (Legacy MSVC)  
  
**********************************************************************  
** Visual Studio 2019 Developer Command Prompt v16.9.4  
** Copyright (c) 2021 Microsoft Corporation  
**********************************************************************  
[vcvarsall.bat] Environment initialized for: 'x64_x86'  
  
C:\Windows\System32>cl /?  
Microsoft (R) C/C++ Optimizing Compiler Version 19.28.29914 for x86  
  
  
  
**### The Error**  
1>C:\Dev\Prj\3rdParty\boost\boost\operators.hpp(154,1): fatal error C1001: Internal compiler error.  
1>(compiler file 'msc1.cpp', line 1588)  
1> To work around this problem, try simplifying or changing the program near the locations listed above.  
1>If possible please provide a repro here: https://developercommunity.visualstudio.com  
1>Please choose the Technical Support command on the Visual C++  
1> Help menu, or open the Technical Support help file for more information  
1>C:\Dev\Prj\3rdParty\boost\boost\operators.hpp(134): message : see reference to class template instantiation 'boost::operators_impl::equality_comparable2<T,U,B>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            U=unsigned int,  
1>            B=boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>  
1>        ]  
1>C:\Dev\Prj\3rdParty\boost\boost\operators.hpp(414): message : see reference to class template instantiation 'boost::operators_impl::less_than_comparable2<T,U,boost::operators_impl::equality_comparable2<T,U,B>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            U=unsigned int,  
1>            B=boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>  
1>        ]  
1>C:\Dev\Prj\3rdParty\boost\boost\operators.hpp(161): message : see reference to class template instantiation 'boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<T>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type  
1>        ]  
1>C:\Dev\Prj\3rdParty\boost\boost\operators.hpp(145): message : see reference to class template instantiation 'boost::operators_impl::equality_comparable1<T,B>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            B=boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>>  
1>        ]  
1>C:\Dev\Prj\3rdParty\boost\boost\operators.hpp(420): message : see reference to class template instantiation 'boost::operators_impl::less_than_comparable1<T,boost::operators_impl::equality_comparable1<T,B>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            B=boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>>  
1>        ]  
1>C:\Dev\Prj\3rdParty\boost\boost\serialization\serialization.hpp(61): message : see reference to class template instantiation 'boost::operators_impl::totally_ordered1<boost::serialization::version_type,boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<T>>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type  
1>        ]  
1>INTERNAL COMPILER ERROR in 'C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.28.29910\bin\HostX86\x86\CL.exe'  
1>    Please choose the Technical Support command on the Visual C++  
1>    Help menu, or open the Technical Support help file for more information  
  
The error is in this part ;  
  
```  
template <class T, class U, class B = operators_detail::empty_base<T> >  
struct equality_comparable2 : B  
{  
     friend bool operator==(const U& y, const T& x) { return x == y; }  
     friend bool operator!=(const U& y, const T& x) { return !static_cast<bool>(x == y); }  
     friend bool operator!=(const T& y, const U& x) { return !static_cast<bool>(y == x); }  
};  
```  
  
Any help please ?????

---

## Comment 1

> Username: shaoheng  
> Created at: 2022-10-20 09:00:19 UTC  
> Url: https://github.com/boostorg/bimap/issues/30#issuecomment-1285181155  

hi, did you fix it and how?

---

## Comment 2

> Username: Amber20011205  
> Created at: 2024-03-12 06:25:54 UTC  
> Url: https://github.com/boostorg/bimap/issues/30#issuecomment-1990827677  

did you fix it and how?
