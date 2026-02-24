# #123 - Boost 1.70.0 compile errors in VS2017 x86 /std:c++17 /clr [Open]

> Username: codingdave  
> Created at: 2019-05-21 13:02:31 UTC  
> Updated at: 2020-11-30 14:57:00 UTC  
> Url: https://github.com/boostorg/type_traits/issues/123  

Hi,  
  
I get compiler errors when trying to compile with 32 bit and CLR using Visual Studio 2017:  
  
> libraries\boost\type_traits\detail\is_function_cxx_11.hpp(388): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(388): error C2953: 'boost::is_function<Ret(Args...) noexcept>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(386): note: see declaration of 'boost::is_function<Ret(Args...) noexcept>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(403): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(403): error C2953: 'boost::is_function<Ret(Args...) noexcept const>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(401): note: see declaration of 'boost::is_function<Ret(Args...) noexcept const>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(418): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(418): error C2953: 'boost::is_function<Ret(Args...) noexcept volatile>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(416): note: see declaration of 'boost::is_function<Ret(Args...) noexcept volatile>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(433): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(433): error C2953: 'boost::is_function<Ret(Args...) noexcept volatile const>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(431): note: see declaration of 'boost::is_function<Ret(Args...) noexcept volatile const>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(449): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(449): error C2953: 'boost::is_function<Ret(Args...) noexcept &>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(447): note: see declaration of 'boost::is_function<Ret(Args...) noexcept &>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(464): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(464): error C2953: 'boost::is_function<Ret(Args...) noexcept const &>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(462): note: see declaration of 'boost::is_function<Ret(Args...) noexcept const &>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(479): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(479): error C2953: 'boost::is_function<Ret(Args...) noexcept volatile &>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(477): note: see declaration of 'boost::is_function<Ret(Args...) noexcept volatile &>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(494): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(494): error C2953: 'boost::is_function<Ret(Args...) noexcept volatile const &>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(492): note: see declaration of 'boost::is_function<Ret(Args...) noexcept volatile const &>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(510): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(510): error C2953: 'boost::is_function<Ret(Args...) noexcept &&>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(508): note: see declaration of 'boost::is_function<Ret(Args...) noexcept &&>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(525): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(525): error C2953: 'boost::is_function<Ret(Args...) noexcept const &&>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(523): note: see declaration of 'boost::is_function<Ret(Args...) noexcept const &&>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(540): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(540): error C2953: 'boost::is_function<Ret(Args...) noexcept volatile &&>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(538): note: see declaration of 'boost::is_function<Ret(Args...) noexcept volatile &&>'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(555): warning C4561: '__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(555): error C2953: 'boost::is_function<Ret(Args...) noexcept volatile const &&>': class template has already been defined  
libraries\boost\type_traits\detail\is_function_cxx_11.hpp(553): note: see declaration of 'boost::is_function<Ret(Args...) noexcept volatile const &&>'

---

## Comment 1

> Username: codingdave  
> Created at: 2019-05-21 13:09:44 UTC  
> Url: https://github.com/boostorg/type_traits/issues/123#issuecomment-494383950  

The PR fixes the issue on my side.

---

## Comment 2

> Username: rcdailey  
> Created at: 2019-06-20 17:58:50 UTC  
> Updated at: 2019-06-20 17:59:09 UTC  
> Url: https://github.com/boostorg/type_traits/issues/123#issuecomment-504123610  

I can confirm this issue exists on vs2019 using boost version 1.70.0. The above patch (https://github.com/codingdave/type_traits/commit/ff1a2967b3ca68a6f8fb229c5ad9bc2cfbeb2852) does fix the issue for me.

---

## Comment 3

> Username: yoavmil  
> Created at: 2020-10-07 14:52:02 UTC  
> Url: https://github.com/boostorg/type_traits/issues/123#issuecomment-704990334  

I have a similar issue. c++17, mcvs19, boost 1.73, x64.  
```  
include\boost\polygon\voronoi_builder.hpp(160,18): error C7626: unnamed class used in typedef name cannot declare members other than non-static data members, member enumerations, or member classes  
```  
couldn't solve it. rolling back to c++14.

---

## Comment 4

> Username: glenfe  
> Created at: 2020-10-07 15:07:57 UTC  
> Url: https://github.com/boostorg/type_traits/issues/123#issuecomment-705000755  

@yoavmil That issue in Boost.Polygon was fixed in develop  but not merged to master for the 1.74 release: https://github.com/boostorg/polygon/commit/5cfcb6cad08dd5413f4a97af6a4fe7c35361de67   
  
I'll merge it for the 1.75 release.

---

## Comment 5

> Username: yoavmil  
> Created at: 2020-10-07 15:20:34 UTC  
> Url: https://github.com/boostorg/type_traits/issues/123#issuecomment-705008851  

Great. thanks for the quick answer

---

## Comment 6

> Username: mw4853  
> Created at: 2020-11-30 14:57:00 UTC  
> Url: https://github.com/boostorg/type_traits/issues/123#issuecomment-735836126  

The patch that @codingdave put up isn't quite correct. It will fix the issue for .NET Framework C++/CLI projects, but not for .NET Core C++/CLI projects.  
  
See:  
Issue: https://github.com/boostorg/type_traits/issues/148  
PR: https://github.com/boostorg/type_traits/pull/149
