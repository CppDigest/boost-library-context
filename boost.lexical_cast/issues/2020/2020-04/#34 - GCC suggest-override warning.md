# #34 - GCC suggest-override warning [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-13 00:07:30 UTC  
> Updated at: 2020-05-03 14:25:13 UTC  
> Closed at: 2020-05-03 10:26:24 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/34  

Warning when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/lexical_cast/bad_lexical_cast.hpp:54:29: warning: ‘virtual const char* boost::bad_lexical_cast::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-05-03 10:26:44 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/34#issuecomment-623088048  

Thanks for the report!

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-05-03 14:25:13 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/34#issuecomment-623118107  

Thank you for fix!
