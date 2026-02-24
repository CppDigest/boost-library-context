# #505 - Temporary MSVC2015 workaround [Closed]

> Username: djowel  
> Created at: 2019-05-05 15:07:51 UTC  
> Updated at: 2019-05-07 00:46:30 UTC  
> Closed at: 2019-05-07 00:46:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/505  

This: https://github.com/boostorg/spirit/commit/793c070d1f8755399bc4ad1df59635102b7e2b57  
  
requires constexpr support. The requirement for X3 is full C++14. However, it's best to warn people of this change and let the old code linger for another release cycle.
