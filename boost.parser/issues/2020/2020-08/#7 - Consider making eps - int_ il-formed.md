# #7 - Consider making eps | int_ il-formed [Closed]

> Username: tzlaine  
> Created at: 2020-08-31 00:40:19 UTC  
> Updated at: 2020-09-01 03:03:38 UTC  
> Closed at: 2020-09-01 03:03:38 UTC  
> Url: https://github.com/boostorg/parser/issues/7  

It should be ill-formed because we know that eps matches anything, and so later alternatives will never be executed.  It is almost certainly a mistake for the user to write that, and catching it at compile time which a helpful `static_assert()` will catch bugs.  There may be more cases than just eps to consider as well.
