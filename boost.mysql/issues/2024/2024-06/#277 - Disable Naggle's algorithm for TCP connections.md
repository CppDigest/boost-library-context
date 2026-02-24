# #277 - Disable Naggle's algorithm for TCP connections [Closed]

> Username: anarthal  
> Created at: 2024-06-06 17:25:04 UTC  
> Updated at: 2024-07-02 16:35:04 UTC  
> Closed at: 2024-07-02 16:35:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/277  

#181 can be avoided by setting `tcp_nodelay` on the underlying socket. While this is a little bit cumbersome in the general case, it's trivial for `any_connection`. It can be relevant to avoid pitfalls when running pipelines (#75).
