# #440 - multi_buffer growth strategy [Closed]

> Username: vinniefalco  
> Created at: 2017-06-08 14:40:09 UTC  
> Updated at: 2017-06-09 22:07:48 UTC  
> Closed at: 2017-06-09 22:07:48 UTC  
> Url: https://github.com/boostorg/beast/issues/440  

`multi_buffer` should grow geometrically not linearly. The constructor argument should be the maximum size.
