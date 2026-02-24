# #66 - channel<T> and channel<void> have a deleted move assignment [Closed]

> Username: anarthal  
> Created at: 2023-08-10 15:01:44 UTC  
> Updated at: 2023-09-04 07:32:29 UTC  
> Closed at: 2023-09-04 07:32:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/66  

They only specify `channel(channel&&) = default`, but not `channel& operator=(channel&&)`. They should specify all the 5 copy/move constructor/assignments and destructor.
