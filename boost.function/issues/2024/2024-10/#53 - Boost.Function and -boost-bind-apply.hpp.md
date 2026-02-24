# #53 - Boost.Function and <boost/bind/apply.hpp> [Closed]

> Username: gast128  
> Created at: 2024-10-12 17:01:37 UTC  
> Updated at: 2024-10-13 08:14:38 UTC  
> Closed at: 2024-10-12 19:12:59 UTC  
> Url: https://github.com/boostorg/function/issues/53  

Recently updated to Boost 1.86 and VS2022 (x64) and we get a weird compilation error:  
  
```  
#include <boost/bind/apply.hpp>  // comment this out to get success  
#include <boost/bind/bind.hpp>  
#include <boost/function.hpp>  
  
int TestArg(int, double)  
{  
   return 0;  
}  
  
  
void f()  
{  
   boost::function<int (int)> fn = boost::bind(&TestArg, boost::placeholders::_1, 1.0);  
}  
```  
Error:  
  
1>D:\Work Sdk\boost_1_86_0\boost\function\function_template.hpp(926,35): error C2977: 'boost::apply': too many template arguments  
1>(compiling source file 'Boost.cpp')  
1>    D:\Work Sdk\boost_1_86_0\boost\bind\apply.hpp(17,26):  
1>    see declaration of 'boost::apply'  
1>    D:\Work Sdk\boost_1_86_0\boost\function\function_template.hpp(926,35):  
1>    the template instantiation context (the oldest one first) is  
1>        D:\Work\Src\Temp\Bla\Boost.cpp(13,34):  
1>        see reference to function template instantiation 'boost::function<int (int)>::function<boost::_bi::bind_t<int,int (__cdecl *)(int,double),boost::_bi::list<boost::arg<1>,boost::_bi::value<T>>>>(Functor,int)' being compiled  
1>        with  
1>        [  
1>            T=double,  
1>            Functor=boost::_bi::bind_t<int,int (__cdecl *)(int,double),boost::_bi::list<boost::arg<1>,boost::_bi::value<double>>>  
1>        ]  
1>            D:\Work\Src\Temp\Bla\Boost.cpp(13,34):  
1>            see the first reference to 'boost::function<int (int)>::function' in 'f'  
1>        D:\Work Sdk\boost_1_86_0\boost\function\function_template.hpp(1084,24):  
1>        see reference to function template instantiation 'boost::function_n<R,int>::function_n<boost::_bi::bind_t<R,int (__cdecl *)(int,double),boost::_bi::list<boost::arg<1>,boost::_bi::value<T>>>>(Functor,int)' being compiled  
1>        with  
1>        [  
1>            R=int,  
1>            T=double,  
1>            Functor=boost::_bi::bind_t<int,int (__cdecl *)(int,double),boost::_bi::list<boost::arg<1>,boost::_bi::value<double>>>  
1>        ]  
1>        D:\Work Sdk\boost_1_86_0\boost\function\function_template.hpp(757,13):  
1>        see reference to function template instantiation 'void boost::function_n<R,int>::assign_to<boost::_bi::bind_t<R,int (__cdecl *)(int,double),boost::_bi::list<boost::arg<1>,boost::_bi::value<T>>>>(Functor)' being compiled  
1>        with  
1>        [  
1>            R=int,  
1>            T=double,  
1>            Functor=boost::_bi::bind_t<int,int (__cdecl *)(int,double),boost::_bi::list<boost::arg<1>,boost::_bi::value<double>>>  
1>        ]  
  
If one comments out the <boost/bind/apply.hpp>it compiles fine. Using auto or std::function also works.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-10-12 17:21:00 UTC  
> Url: https://github.com/boostorg/function/issues/53#issuecomment-2408632682  

That's a pretty annoying bug in MSVC; I thought it was no longer an issue in newer versions / later standard modes / `/permissive-`, but it looks like none of these affect this particular instance of it. :-/  
  
You should report this to MS as a bug; in the meantime I'll try to figure out a way to avoid hitting it.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-10-12 19:14:30 UTC  
> Url: https://github.com/boostorg/function/issues/53#issuecomment-2408665551  

I could think of nothing better than renaming the nested `apply` class template to avoid the conflict (which by the way only happens on msvc-14.3 / VS2022 with `/permissive-`, not on earlier compiler versions.)

---

## Comment 3

> Username: gast128  
> Created at: 2024-10-13 08:14:36 UTC  
> Url: https://github.com/boostorg/function/issues/53#issuecomment-2408877646  

Thx. I am a bit reluctant to enter a bug reports since MS has quite the habit with closing them as not reproducible,; not important or not our fault. I tried to reproduce it in compiler explorer but it seems that you cannot use external libraries for the MSVC compiler (it works when using GCC).
