# #157 - pydocca: doubly-nested types get an incorrect link [Closed]

> Username: anarthal  
> Created at: 2024-08-14 12:53:41 UTC  
> Updated at: 2024-08-21 01:29:39 UTC  
> Closed at: 2024-08-21 01:29:39 UTC  
> Url: https://github.com/boostorg/docca/issues/157  

Types with several level of nesting get incorrect links. Please see the reproduction in this gist:   
https://gist.github.com/anarthal/763529a57683f3c20ba78ecec290fb47  
  
The problem is that the `link` macro is not handling this case correctly.  
  
This pattern is unfortunately common in asio libs, when defining nested templates to rebind allocators.
