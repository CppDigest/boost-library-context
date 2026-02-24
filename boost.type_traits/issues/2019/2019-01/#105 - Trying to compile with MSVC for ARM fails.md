# #105 - Trying to compile with MSVC for ARM fails [Closed]

> Username: janisozaur  
> Created at: 2019-01-08 21:49:00 UTC  
> Updated at: 2019-01-10 17:57:22 UTC  
> Closed at: 2019-01-10 17:57:22 UTC  
> Url: https://github.com/boostorg/type_traits/issues/105  

Trying to build `boost-regex` with vcpkg with `arm-static-windows` triplet (modified `arm-windows` with static linkage) fails with following errors:  
```  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(106): error C2953: 'boost::is_function<Ret(Args...)>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(36): note: see declaration of 'boost::is_function<Ret(Args...)>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(109): error C2953: 'boost::is_function<Ret(Args...)>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(36): note: see declaration of 'boost::is_function<Ret(Args...)>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(123): error C2953: 'boost::is_function<Ret(Args...) const>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(41): note: see declaration of 'boost::is_function<Ret(Args...) const>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(126): error C2953: 'boost::is_function<Ret(Args...) const>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(41): note: see declaration of 'boost::is_function<Ret(Args...) const>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(140): error C2953: 'boost::is_function<Ret(Args...) volatile>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(46): note: see declaration of 'boost::is_function<Ret(Args...) volatile>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(143): error C2953: 'boost::is_function<Ret(Args...) volatile>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(46): note: see declaration of 'boost::is_function<Ret(Args...) volatile>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(157): error C2953: 'boost::is_function<Ret(Args...) volatile const>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(51): note: see declaration of 'boost::is_function<Ret(Args...) volatile const>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(160): error C2953: 'boost::is_function<Ret(Args...) volatile const>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(51): note: see declaration of 'boost::is_function<Ret(Args...) volatile const>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(175): error C2953: 'boost::is_function<Ret(Args...) &>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(58): note: see declaration of 'boost::is_function<Ret(Args...) &>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(178): error C2953: 'boost::is_function<Ret(Args...) &>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(58): note: see declaration of 'boost::is_function<Ret(Args...) &>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(192): error C2953: 'boost::is_function<Ret(Args...) const &>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(63): note: see declaration of 'boost::is_function<Ret(Args...) const &>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(195): error C2953: 'boost::is_function<Ret(Args...) const &>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(63): note: see declaration of 'boost::is_function<Ret(Args...) const &>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(209): error C2953: 'boost::is_function<Ret(Args...) volatile &>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(68): note: see declaration of 'boost::is_function<Ret(Args...) volatile &>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(212): error C2953: 'boost::is_function<Ret(Args...) volatile &>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(68): note: see declaration of 'boost::is_function<Ret(Args...) volatile &>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(226): error C2953: 'boost::is_function<Ret(Args...) volatile const &>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(73): note: see declaration of 'boost::is_function<Ret(Args...) volatile const &>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(229): error C2953: 'boost::is_function<Ret(Args...) volatile const &>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(73): note: see declaration of 'boost::is_function<Ret(Args...) volatile const &>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(244): error C2953: 'boost::is_function<Ret(Args...) &&>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(80): note: see declaration of 'boost::is_function<Ret(Args...) &&>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(247): error C2953: 'boost::is_function<Ret(Args...) &&>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(80): note: see declaration of 'boost::is_function<Ret(Args...) &&>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(261): error C2953: 'boost::is_function<Ret(Args...) const &&>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(85): note: see declaration of 'boost::is_function<Ret(Args...) const &&>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(264): error C2953: 'boost::is_function<Ret(Args...) const &&>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(85): note: see declaration of 'boost::is_function<Ret(Args...) const &&>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(278): error C2953: 'boost::is_function<Ret(Args...) volatile &&>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(90): note: see declaration of 'boost::is_function<Ret(Args...) volatile &&>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(281): error C2953: 'boost::is_function<Ret(Args...) volatile &&>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(90): note: see declaration of 'boost::is_function<Ret(Args...) volatile &&>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(295): error C2953: 'boost::is_function<Ret(Args...) volatile const &&>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(95): note: see declaration of 'boost::is_function<Ret(Args...) volatile const &&>'  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(298): error C2953: 'boost::is_function<Ret(Args...) volatile const &&>': class template has already been defined  
C:\Users\user\vcpkg\installed\arm-static-windows\include\boost/type_traits/detail/is_function_cxx_11.hpp(95): note: see declaration of 'boost::is_function<Ret(Args...) volatile const &&>'  
```  
  
You can find full log as provided by vcpkg here: https://gist.github.com/janisozaur/cf45cb9990da6044ef75621e9e104b3f

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-01-09 12:09:05 UTC  
> Url: https://github.com/boostorg/type_traits/issues/105#issuecomment-452674694  

Thanks for the report, just trying to get the tooling installed now so I can reproduce, is this a native arm app or CLR?

---

## Comment 2

> Username: janisozaur  
> Created at: 2019-01-09 13:06:24 UTC  
> Url: https://github.com/boostorg/type_traits/issues/105#issuecomment-452689773  

Native. It should be possible to reproduce with passing `architecture=arm` to one of `appveyor.yml` entries, I can submit PR a bit later.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-01-10 17:57:22 UTC  
> Url: https://github.com/boostorg/type_traits/issues/105#issuecomment-453192177  

Should now be fixed in develop.
