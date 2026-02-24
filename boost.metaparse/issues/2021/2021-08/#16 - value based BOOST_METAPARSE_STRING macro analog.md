# #16 - [Feature request] value based BOOST_METAPARSE_STRING macro analog [Closed]

> Username: denzor200  
> Created at: 2021-08-12 15:56:58 UTC  
> Updated at: 2021-09-11 17:12:23 UTC  
> Closed at: 2021-09-11 17:12:23 UTC  
> Url: https://github.com/boostorg/metaparse/issues/16  

Hi. I am using `boost::metaparse::string` for value-based metaprogramming.  
For convenience, I created a macro for myself:  
```  
#ifdef BOOST_METAPARSE_MAKE_STRING  
#error BOOST_METAPARSE_MAKE_STRING already defined  
#endif  
#define BOOST_METAPARSE_MAKE_STRING(S) (BOOST_METAPARSE_STRING(S){})  
```  
This macro allows me to write such statements succinctly and without unnecessary parentheses:  
```   
field<int, (name=BOOST_METAPARSE_MAKE_STRING("id"))> index;  
field<std::string, (name=BOOST_METAPARSE_MAKE_STRING("user"))> username;  
```  
In modern C++(example above is C++17), the value-based metaprogramming approach is very often used. I believe that this macro will be useful not only for me. Why not add it to the library?

---

## Comment 1

> Username: sabel83  
> Created at: 2021-08-30 08:50:24 UTC  
> Url: https://github.com/boostorg/metaparse/issues/16#issuecomment-908163871  

Hi. I've added the macro on the `string_value` branch in my fork of this repository (see https://github.com/sabel83/metaparse/tree/string_value). Note that I've renamed it to `BOOST_METAPARSE_STRING_VALUE` as its purpose is to provide a value.  
  
Please take a look and let me know if this works for you.

---

## Comment 2

> Username: denzor200  
> Created at: 2021-08-30 19:22:38 UTC  
> Url: https://github.com/boostorg/metaparse/issues/16#issuecomment-908622209  

@sabel83 Cool! This is what i need. Thanks

---

## Comment 3

> Username: sabel83  
> Created at: 2021-09-11 17:12:23 UTC  
> Url: https://github.com/boostorg/metaparse/issues/16#issuecomment-917440701  

It is now merged to the `master` branch and will be part of the next release. Thank you for your suggestion.
