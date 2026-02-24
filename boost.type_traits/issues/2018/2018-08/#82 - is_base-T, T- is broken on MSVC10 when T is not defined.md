# #82 - is_base<T, T> is broken on MSVC10 when T is not defined [Open]

> Username: jzmaddock  
> Created at: 2018-08-02 11:38:04 UTC  
> Updated at: 2018-08-02 11:38:04 UTC  
> Url: https://github.com/boostorg/type_traits/issues/82  

Currently on Windows with MSVC10 boost defines BOOST_IS_BASE_OF (which is used in is_base_and_derived, which is used in is_base_of) as follows:  
```  
#define BOOST_IS_BASE_OF(T,U) (__is_base_of(T,U) && !is_same<T,U>::value)  
```  
But microsoft's intrinsic __is_base_of cannot be used with the same type (i.e. T = U) when it (the type) is not defined.  
  
When the following is compiled  
```  
class A;  
typedef typename boost::is_base_of<A, A>::type x;  
```  
you get an error message:  
```  
...\include\boost\type_traits\is_base_and_derived.hpp(228) : error C2139: 'A' : an undefined class is not allowed as an argument to compiler intrinsic type trait '__is_base_of'  
...\include\boost\type_traits\is_base_of.hpp(29) : see reference to class template instantiation 'boost::detail::is_base_and_derived_impl<B,D>' being compiled  
        with  
        [  
            B=A,  
            D=A  
        ]  
```  
The same code compiles on gcc-4.8.2 and clang-3.6.  
  
Moved from https://svn.boost.org/trac10/ticket/11422
