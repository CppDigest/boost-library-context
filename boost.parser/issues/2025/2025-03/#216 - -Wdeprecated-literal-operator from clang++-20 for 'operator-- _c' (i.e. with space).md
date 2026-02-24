# #216 - -Wdeprecated-literal-operator from clang++-20 for 'operator"" _c' (i.e. with space) [Closed]

> Username: MichaelChirico  
> Created at: 2025-03-12 17:10:49 UTC  
> Updated at: 2025-03-14 00:04:30 UTC  
> Closed at: 2025-03-14 00:04:30 UTC  
> Url: https://github.com/boostorg/parser/issues/216  

https://github.com/boostorg/parser/blob/78bc141d5f8859c3a4a51f1e0291898b835fc676/include/boost/parser/tuple.hpp#L131  
  
The fix _can_ be trivial, but I think there's some back-compatibility considerations I lack the context to adjudicate, see sister issues e.g.  
  
https://github.com/boostorg/hana/pull/521
