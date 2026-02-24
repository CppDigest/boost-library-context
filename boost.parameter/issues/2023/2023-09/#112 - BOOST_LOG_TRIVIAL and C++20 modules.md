# #112 - BOOST_LOG_TRIVIAL and C++20 modules [Closed]

> Username: Patschkowski  
> Created at: 2023-09-07 00:59:32 UTC  
> Updated at: 2023-09-27 10:57:16 UTC  
> Closed at: 2023-09-07 09:13:36 UTC  
> Url: https://github.com/boostorg/parameter/issues/112  

Dear all,  
  
I am facing the following challenge when using the BOOST_LOG_TRIVIAL functionality from C++20 modules.  
  
```cpp  
// foo.ixx  
module;  
  
#include <boost/log/trivial.hpp>  
  
export module foo;  
  
export namespace foo {  
  
template<typename T>  
void bar()  
{  
    BOOST_LOG_TRIVIAL(info) << "Hello, World!";  
}  
  
}  
```  
  
```cpp  
// main.cpp  
#include <compare>  
  
import foo;  
  
int main()  
{  
    foo::bar<int>();  
    return 0;  
}  
```  
  
This code fails to compile on my Visual Studio 2022 with the following error message.  
  
> boost\log\keywords\severity.hpp(32,1): error C7631: 'boost::log::v2_mt_nt6::keywords::`anonymous-namespace'::severity': variable with internal linkage declared but not defined   
  
My current understanding of this issue is that, based on [Understanding C++ Modules: Part 3: Linkage and Fragments](https://vector-of-bool.github.io/2019/10/07/modules-3.html), `severity` has module internal linkage because it is in an anonymous namespace. But as `bar` is a template, it must also be available to importers that instantiate the template, which is not allowed. So the code fails to compile.  
  
Is there any way around this or can the library be improved to also support this scenario?  
  
Thanks in advance,  
Felix

---

## Comment 1

> Username: Lastique  
> Created at: 2023-09-07 09:13:36 UTC  
> Url: https://github.com/boostorg/parameter/issues/112#issuecomment-1709786242  

1. Boost.Log uses [Boost.Parameter](https://github.com/boostorg/parameter/) to define its parameter keywords. Since the problem is with a parameter keyword definition, I moved the issue there.  
2. The error message doesn't make sense. You can see how the keyword is defined [here](https://github.com/boostorg/parameter/blob/6538609cf5e390b6e7cbcb73173d86d18af73281/include/boost/parameter/keyword.hpp#L716-L717), and that is a variable *definition*, given that there is an initializer. I think this is a compiler bug and should be reported to Microsoft.  
3. I don't have experience with modules, but I'm pretty sure exported functions must be able to use symbols from anonymous namespaces in their body. What symbols the function body uses has no effect on the function's linkage, otherwise you wouldn't be able to use objects and functions with internal linkage at all. So I'm sure the anonymous namespace is not a problem, not wrt. symbol linkage.  
4. Unfortunately, I don't have a workaround for you, other than to not use modules or try a different compiler.

---

## Comment 2

> Username: Patschkowski  
> Created at: 2023-09-27 09:49:47 UTC  
> Url: https://github.com/boostorg/parameter/issues/112#issuecomment-1737073383  

I think this should be reconsidered based on the Microsoft feedback given here: https://developercommunity.visualstudio.com/t/error-C7631:-when-instantiating-template/10476144  
  
But also it links to this issue: https://github.com/boostorg/parameter/issues/111

---

## Comment 3

> Username: Lastique  
> Created at: 2023-09-27 10:57:16 UTC  
> Url: https://github.com/boostorg/parameter/issues/112#issuecomment-1737169327  

I do not agree with their feedback, I left a comment there. And if it is indeed the intended behavior that is mandated by the C++ standard then I consider modules specification broken.
