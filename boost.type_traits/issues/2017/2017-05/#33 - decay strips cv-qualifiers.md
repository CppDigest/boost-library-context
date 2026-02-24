# #33 - decay strips cv-qualifiers [Closed]

> Username: K-ballo  
> Created at: 2017-05-16 21:54:40 UTC  
> Updated at: 2018-08-06 12:22:39 UTC  
> Closed at: 2018-08-06 12:22:38 UTC  
> Url: https://github.com/boostorg/type_traits/issues/33  

Since d3ac8b95c4cbc8876746b573daf3fa60446cfd27 `boost::decay` strips cv-qualifiers, but the documentation was never adjusted accordingly:  
  
> Let `U` be the result of `remove_reference<T>::type`, then if `U` is an array type, the result is `remove_extent<U>::type*`, otherwise if `U` is a function type then the result is `U*`, otherwise the result is `U`.
