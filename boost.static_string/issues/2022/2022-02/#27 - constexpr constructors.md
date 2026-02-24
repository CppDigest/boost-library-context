# #27 - constexpr constructors [Closed]

> Username: timblechmann  
> Created at: 2022-02-25 01:38:56 UTC  
> Updated at: 2022-11-01 22:26:07 UTC  
> Closed at: 2022-11-01 22:26:07 UTC  
> Url: https://github.com/boostorg/static_string/issues/27  

there are a few small problems with using `static_string` in constexpr.  
  
one cannot create a static_string from a character literal:  
```  
constexpr boost::static_string<16> s{"yada"};  
```  
  
one cannot return a static_string from a constexpr function:  
```  
constexpr boost::static_string<16> gen()  
{  
    boost::static_string<16> ret;  
    return ret;  
}  
  
constexpr auto x = gen();  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-02-25 16:28:13 UTC  
> Url: https://github.com/boostorg/static_string/issues/27#issuecomment-1051002418  

Does that work in C++11 ?

---

## Comment 2

> Username: timblechmann  
> Created at: 2022-02-26 03:05:47 UTC  
> Updated at: 2022-02-26 03:07:57 UTC  
> Url: https://github.com/boostorg/static_string/issues/27#issuecomment-1051520158  

> Does that work in C++11 ?  
  
afaict it requires c++14:  
```  
constexpr std::array<char, 2> foo()   
{  
    std::array<char, 2> x {"x"};  
    return x;  
}  
  
constexpr std::array<char, 2> x = foo();  
```  
  
there seem to be two culprits:  
* not the full struct is initialised  
* some of the assignment seems to go into codepaths which aren't constexpr safe

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-11-01 22:26:07 UTC  
> Url: https://github.com/boostorg/static_string/issues/27#issuecomment-1299306900  

This should have been resolved in #32
