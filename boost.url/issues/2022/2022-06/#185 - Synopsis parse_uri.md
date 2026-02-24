# #185 - Synopsis parse_uri [Closed]

> Username: alandefreitas  
> Created at: 2022-06-05 18:45:12 UTC  
> Updated at: 2022-06-09 20:07:23 UTC  
> Closed at: 2022-06-09 20:07:23 UTC  
> Url: https://github.com/boostorg/url/issues/185  

The synopsis for parse_uri (  
https://master.url.cpp.al/url/ref/boost__urls__parse_uri.html) says:  
  
Exception safety: throws nothing.  
>  
And the line below it says that the function throws std::length_error when  
the input is too long. It looks like a bug in specs.
