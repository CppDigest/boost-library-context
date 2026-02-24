# #809 - sanitize_uri moves host to path [Closed]

> Username: alandefreitas  
> Created at: 2024-01-02 18:38:54 UTC  
> Updated at: 2024-01-03 20:57:30 UTC  
> Closed at: 2024-01-03 20:57:30 UTC  
> Url: https://github.com/boostorg/url/issues/809  

> I was trying out the example in https://www.boost.org/doc/libs/1_84_0/libs/url/doc/html/url/examples/sanitizing_urls.html and it doesn't seem quite right.   
  
> For example, `sanitize_uri("https://a.b.c/a/b/c/[d]")` returns `https:a.b.c/a/b/c/%5Bd%5D`, which is almost right but `a.b.c/a/b/c/%5Bd%5D` is all treated as path instead of `a.b.c` as authority and `a/b/c/%5Bd%5D` as path
