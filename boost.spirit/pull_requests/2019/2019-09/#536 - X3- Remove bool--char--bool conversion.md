# #536 X3: Remove bool->char->bool conversion [Merged]

> Username: Kojoley  
> Created at: 2019-09-20 21:25:24 UTC  
> Updated at: 2019-12-01 00:22:19 UTC  
> Merged at: 2019-09-21 00:34:33 UTC  
> Closed at: 2019-09-21 00:34:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/536  

MSVC barfs on implicit conversion to `bool` here, and it is indeed suspicious.  
  
I have no idea why `in_set` return type is a char type, the method name itself suggests that it returns boolean value. The only used template parameter type seems to be `basic_set` and its `test` method return `bool`. https://github.com/boostorg/spirit/blob/9cf7a3ef739d57d091ed3812a54481cc877b0a56/include/boost/spirit/home/support/char_set/basic_chset.hpp#L35-L39  
The usages of `in_set` method also seem to expect a boolean value from it. https://github.com/boostorg/spirit/blob/9cf7a3ef739d57d091ed3812a54481cc877b0a56/include/boost/spirit/home/x3/char/char_set.hpp#L99

---
