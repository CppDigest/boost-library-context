# #115 - "Taking control of SFINAE" does not showcase Optional's strengths properly [Open]

> Username: ldionne  
> Created at: 2015-06-14 17:58:26 UTC  
> Updated at: 2015-06-14 17:58:26 UTC  
> Url: https://github.com/boostorg/hana/issues/115  

It was pointed on the [Boost.Devel mailing list](http://article.gmane.org/gmane.comp.lib.boost.devel/261160) that the "Taking control of SFINAE" section of the tutorial could have been achieved more simply with `overload_linearly`:  
  
``` c++  
auto optionalToString = hana::overload_linearly(  
    [](auto&& x) -> decltype(x.toString()) { return x.toString(); },  
    [](auto&&) -> std::string { return "toString not defined"; }  
);  
```  
  
My initial goal was to showcase `Optional`'s strengths, but clearly that example is not the right one.
