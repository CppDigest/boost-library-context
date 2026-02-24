# #85 - string::substr uncertainty about storage [Closed]

> Username: vinniefalco  
> Created at: 2020-05-02 00:47:02 UTC  
> Updated at: 2020-05-02 02:40:16 UTC  
> Closed at: 2020-05-02 02:40:16 UTC  
> Url: https://github.com/boostorg/json/issues/85  

Right now `string::substr` returns a string using the default storage. We might need to simply remove this overload. If users want a copy they can use `subview` in a constructor call, possibly explicitly specifying a storage pointer.
