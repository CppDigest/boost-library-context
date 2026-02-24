# #273 - type support or constexpr support [Closed]

> Username: wang-jinbo  
> Created at: 2020-10-25 07:15:29 UTC  
> Updated at: 2020-10-26 13:15:42 UTC  
> Closed at: 2020-10-26 13:15:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/273  

When I use concept to regulate my program, I'm confusing, first of all, I wanna my program to work on numbers that can really be divided, for example:  
1.*long double*  
2.*boost::rational<boost::multiprecision::int128_t>*  
3.*boost::multiprecision::boost::multiprecision::cpp_bin_float_quad*  
I'm confused how to write requires() statement, I tried  
```C++  
//T is the type  
requires(T(1) / T(2) != T(0))  
```  
But boost::multiprecision::cpp_bin_float_quad is not a literal-type  
At this time I want to write a type trait on my own to include boost::rational, say, boo::is_rational_v, and I write  
```C++  
required(boo::is_rational_v || (std::numeric_limits<v>::is_specialised && !std::numeric_limits<v>::is_integer))  
```  
But I have a question that is there a more elegant way to combine(std::numeric_limits<v>::is_specialised && !std::numeric_limits<v>::is_integer), **but in fact, I'm not sure this two statement can judge multiprecision float**, and even further, write a concept of these three

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-10-26 13:15:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/273#issuecomment-716538376  

I confess I'm not familiar with C++20's concepts yet, but good old C++11 can work just fine:  
  
* If you really want to detect support for just division then see: https://www.boost.org/doc/libs/1_74_0/libs/type_traits/doc/html/boost_typetraits/reference/has_divides.html  
  
* If you want number categorisation then you could use `boost::multiprecision::number_category`, see https://www.boost.org/doc/libs/1_74_0/libs/multiprecision/doc/html/boost_multiprecision/ref/number.html#boost_multiprecision.ref.number.traits_class_support  
  
* Almost any arbitrary expression can be tested for with https://www.boost.org/doc/libs/1_74_0/libs/type_traits/doc/html/boost_typetraits/reference/is_detected.html  
  
All of the above would work with enable_if in C++11, or concepts as well no doubt.
