# #856 - set_params has no enconding_opts [Closed]

> Username: alandefreitas  
> Created at: 2024-08-15 14:55:28 UTC  
> Updated at: 2024-08-20 00:48:55 UTC  
> Closed at: 2024-08-20 00:48:55 UTC  
> Url: https://github.com/boostorg/url/issues/856  

[params](https://www.boost.org/doc/libs/1_85_0/libs/url/doc/html/url/ref/boost__urls__url/params.html) lets you set the encoding_opts. For this component, the most important setting is `space_as_plus` and this depends on the application.   
  
The reason params lets you set it and set_params doesn't is only historical. `set_params` was later implemented as a convenience function based on params. So, we ended up not including this overload. But we should have.
