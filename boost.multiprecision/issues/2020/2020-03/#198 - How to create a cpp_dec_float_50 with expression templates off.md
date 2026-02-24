# #198 - How to create a cpp_dec_float_50 with expression templates off? [Closed]

> Username: pabristow  
> Created at: 2020-03-05 14:49:09 UTC  
> Updated at: 2020-03-05 17:45:00 UTC  
> Closed at: 2020-03-05 17:45:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/198  

I am testing a prototype Dirichlet distribution function for multiprecision-worthiness.  
  
show_ditichlet is a function to display some parameters of the distribution.  It works as expected for fundamental types and for these multiprecision types  
  
 ` show_dirichlet<cpp_bin_float_quad>();  
  show_dirichlet<cpp_bin_float_100>();  
`  
    
But fails for   
`  show_dirichlet<cpp_dec_float_50>();  
  // error C2338: You can not assign to a Boost.Multiprecision expression template: did you inadvertantly store an expression template in a "auto" variable?  Or pass an expression to a template function with deduced temnplate arguments?  
`  
  
I'm not sure how I did that ;-)  but I tried to turn expression templates off using  
  typedef boost::multiprecision::number<boost::multiprecision::cpp_dec_float_50,   
  
  
This brief demo of my type works OK for   
  
`boost::multiprecision::et_off> cpp_dec_float_50_off;  
  cpp_dec_float_50_off a("1.234");  
  std::cout << "a  = " << a << std::endl; // a  = 1.234  
  show_value(a); // 1.234000000000000000000000000000000000000000000000000000000000000000000000  
`  
but when I try  
`  show_dirichlet<cpp_dec_float_50_off>();`  
  
I get a blizzard of errors shown below  
  
Should I simply not try to test using dec_float (since bin_float examples all work fine)?  
  
Or is there some simple error that a simple user is making in defining a dec_float_et_off type?  
  
Using MSVC preview 16.5 /c++14, 17, and std:latest  
  
Errors start with:  
  
 `dirichlet_basic_examples.cpp  
I:\boost\boost\multiprecision\detail\number_base.hpp(302,26): error C2039: 'float_types': is not a member of 'boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50,int32_t,void>,boost::multiprecision::et_on>'  
I:\boost\boost\multiprecision\cpp_dec_float.hpp(3035): message : see declaration of 'boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50,int32_t,void>,boost::multiprecision::et_on>'  
I:\boost\boost\multiprecision\detail\number_base.hpp(332): message : see reference to class template instantiation 'boost::multiprecision::detail::canonical_imp<Val,Backend,boost::mpl::int_<2>>' being compiled  
          with  
          [  
              Val=largest_float,  
              Backend=boost::multiprecision::cpp_dec_float_50  
          ]  
I:\boost\boost\type_traits\is_convertible.hpp(490): message : see reference to class template instantiation 'boost::multiprecision::detail::canonical<From,Backend>' being compiled  
          with  
          [  
              From=largest_float,  
              Backend=boost::multiprecision::cpp_dec_float_50  
          ]  
I:\boost\boost\multiprecision\number.hpp(68): message : while compiling class template member function 'boost::multiprecision::number<boost::multiprecision::cpp_dec_float_50,boost::multiprecision::et_off>::number(const V &,boost::enable_if_c<boost::is_convertible<detail::canonical<V,Backend>::type,Backend>::value&&!boost::multiprecision::detail::is_restricted_conversion<detail::canonical<V,Backend>::type,Backend>::value,void>::type *) noexcept(<expr>)'  
          with  
          [  
              Backend=boost::multiprecision::cpp_dec_float_50  
          ]  
I:\Cpp\math\dirichlet_examples\dirichlet_basic_examples.cpp(150): message : see reference to class template instantiation 'boost::is_convertible<largest_float,RealType>' being compiled  
          with  
          [  
              RealType=cpp_dec_float_50_off  
          ]  
I:\Cpp\math\dirichlet_examples\dirichlet_basic_examples.cpp(219): message : see reference to function template instantiation 'void show_dirichlet<cpp_dec_float_50_off>(void)' being compiled  
I:\boost\boost\multiprecision\detail\number_base.hpp(302,1): error C2146: syntax error: missing '>' before identifier 'float_types'  
I:\boost\boost\multiprecision\detail\number_base.hpp(303): error C2062: type 'unknown-type' unexpected  
I:\boost\boost\multiprecision\detail\number_base.hpp(303,20): error C2039: 'type': is not a member of '`global namespace''  
I:\boost\boost\multiprecision\detail\number_base.hpp(303,1): error C2238: unexpected token(s) preceding ';'  
I:\boost\boost\multiprecision\detail\number_base.hpp(304,48): error C2039: 'float_types': is not a member of 'boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50,int32_t,void>,boost::multiprecision::et_on>'  
I:\boost\boost\multiprecision\cpp_dec_float.hpp(3035): message : see declaration of 'boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50,int32_t,void>,boost::multiprecision::et_on>'  
I:\boost\boost\multiprecision\detail\number_base.hpp(304,1): error C2146: syntax error: missing '>' before identifier 'float_types'  
I:\boost\boost\multiprecision\detail\number_base.hpp(304): error C2062: type 'unknown-type' unexpected  
I:\boost\boost\multiprecision\detail\number_base.hpp(304,62): error C2039: 'type': is not a member of '`global namespace''  
I:\boost\boost\multiprecision\detail\number_base.hpp(304,1): error C2238: unexpected token(s) preceding ';'  
I:\boost\boost\multiprecision\detail\number_base.hpp(305,49): error C2065: 'iter_type': undeclared identifier  
I:\boost\boost\multiprecision\detail\number_base.hpp(305,60): error C2065: 'end_type': undeclared identifier  
I:\boost\boost\multiprecision\detail\number_base.hpp(305,41): error C2923: 'boost::is_same': 'iter_type' is not a valid template type argument for parameter 'T'  
I:\boost\boost\multiprecision\detail\number_base.hpp(305,41): error C2923: 'boost::is_same': 'end_type' is not a valid template type argument for parameter 'U'  
I:\boost\boost\multiprecision\detail\number_base.hpp(305,9): error C2065: 'iter_type': undeclared identifier  
I:\boost\boost\multiprecision\detail\number_base.hpp(305,3): error C2923: 'boost::mpl::deref': 'iter_type' is not a valid template type argument for parameter 'Iterator'  
I:\boost\boost\multiprecision\detail\number_base.hpp(305,41): error C3203: 'is_same': unspecialized class template can't be used as a template argument for template parameter 'C', expected a real type  
I:\boost\boost\multiprecision\detail\number_base.hpp(305,3): error C3203: 'deref': unspecialized class template can't be used as a template argument for template parameter 'F2', expected a real type  
I:\boost\boost\mpl\if.hpp(63,1): error C2825: 'T1': must be a class or namespace when followed by '::'  
I:\boost\boost\mpl\eval_if.hpp(40): message : see reference to class template instantiation 'boost::mpl::if_<C,F1,F2>' being compiled  
          with  
...  
  
`

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-03-05 16:38:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/198#issuecomment-595325327  

Should be:  
  
```  
typedef boost::multiprecision::number<boost::multiprecision::cpp_dec_float_50::backend_type, boost::multiprecision::et_off> cpp_dec_float_50_off;  
```

---

## Comment 2

> Username: pabristow  
> Created at: 2020-03-05 17:45:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/198#issuecomment-595358277  

Correct, as ever.  Now runs my multiprecision tests of Dirchlet distribution OK.  Thanks.
