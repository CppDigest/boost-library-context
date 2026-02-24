# #105 - optimization for trivial T's [Open]

> Username: akrzemi1  
> Created at: 2022-09-06 23:02:53 UTC  
> Updated at: 2022-09-06 23:03:08 UTC  
> Url: https://github.com/boostorg/optional/issues/105  

Currently optional provides optimized storage for scalar types:  
https://github.com/boostorg/optional/blob/develop/include/boost/optional/optional.hpp#L900  
  
Generalize it for any trivial type that is move/copy constructible/assignable, as per description in https://www.boost.org/doc/libs/1_80_0/libs/optional/doc/html/boost_optional/tutorial/gotchas/moved_from__optional_.html
