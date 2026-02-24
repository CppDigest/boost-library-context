# #407 - -Wdeprecated-literal-operator from clang++-20 for 'operator"" _c' (i.e. with space) [Closed]

> Username: MichaelChirico  
> Created at: 2025-03-12 17:00:21 UTC  
> Updated at: 2025-06-05 14:54:55 UTC  
> Closed at: 2025-06-05 14:54:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/407  

https://github.com/boostorg/histogram/blob/66842660c0bad0ce80c6db5fe68c7d7e4ec00be4/include/boost/histogram/literals.hpp#L26  
  
The fix _can_ be trivial, but I think there's some back-compatibility considerations I lack the context to adjudicate, see sister issues e.g.  
  
https://github.com/boostorg/hana/pull/521
