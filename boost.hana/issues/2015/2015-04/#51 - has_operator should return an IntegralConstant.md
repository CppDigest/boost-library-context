# #51 - has_operator should return an IntegralConstant [Closed]

> Username: ldionne  
> Created at: 2015-04-19 17:44:29 UTC  
> Updated at: 2015-04-22 13:42:03 UTC  
> Closed at: 2015-04-22 13:42:03 UTC  
> Url: https://github.com/boostorg/hana/issues/51  

More generally, everything in `core` should use the same consistent interface. Unfortunately, this raises issues of circular dependencies. Or perhaps `core` should be minimal and not use `IntegralConstant`s at all?
