# #458 - Fix multi_buffer move constructor with alloc [Closed]

> Username: vinniefalco  
> Created at: 2017-06-09 23:51:57 UTC  
> Updated at: 2017-06-12 02:47:42 UTC  
> Closed at: 2017-06-12 02:47:42 UTC  
> Url: https://github.com/boostorg/beast/issues/458  

`multi_buffer` move constructor which takes an allocator clobbers its copy of the passed allocator when the moved-from allocator is equal to the new one (in `move_assign`)
