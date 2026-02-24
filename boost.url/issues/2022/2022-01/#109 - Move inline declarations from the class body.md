# #109 - Move inline declarations from the class body [Closed]

> Username: alandefreitas  
> Created at: 2022-01-10 20:55:20 UTC  
> Updated at: 2022-03-15 18:38:40 UTC  
> Closed at: 2022-03-15 18:38:40 UTC  
> Url: https://github.com/boostorg/url/issues/109  

This issue resumes the discussion from https://github.com/CPPAlliance/url/pull/95#discussion_r780637123  
  
According to this [isocpp guideline](https://isocpp.org/wiki/faq/inline-functions#where-to-put-inline-keyword), when a class is intended to be highly reused and your reusers will read your header file to determine observable semantics or external behavior, it is best to put the inline keyword next to the declaration next to the definition outside the class body.   
  
For instance, [`asio/io_context.hpp`](https://github.com/boostorg/asio/blob/develop/include/boost/asio/io_context.hpp) does not contain any `inline` keyword. All `inline` keywords are in [`asio/impl/io_context.hpp`](https://github.com/boostorg/asio/blob/develop/include/boost/asio/impl/io_context.hpp). This happens [throughout the project](https://github.com/boostorg/asio/search?q=inline) wherever the declaration and definition are separated.    
  
In Boost.URL, this would involve moving the `inline` keywords in declarations such as  
  
https://github.com/CPPAlliance/url/blob/756b6b6dbc896674620545423fd11df9b506d415/include/boost/url/params.hpp#L166  
  
to their definitions in  
  
https://github.com/CPPAlliance/url/blob/756b6b6dbc896674620545423fd11df9b506d415/include/boost/url/impl/params.hpp#L288  
  
This separation:  
  
- makes life easier and safer for your class’s reusers  
- moves an implementation detail that does not change the observable semantics (the “meaning”) of a call  
- avoids warnings in common tools for static analysis  
- simplifies the possibility of implementing patterns such as `BOOST_ASIO_SEPARATE_COMPILATION`
