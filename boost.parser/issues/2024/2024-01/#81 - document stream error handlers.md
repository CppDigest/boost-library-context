# #81 - document stream error handlers [Closed]

> Username: akrzemi1  
> Created at: 2024-01-27 11:12:23 UTC  
> Updated at: 2024-01-27 22:06:58 UTC  
> Closed at: 2024-01-27 22:06:58 UTC  
> Url: https://github.com/boostorg/parser/issues/81  

The documentaiton for the `stream_error_handler` is confusing:  
https://tzlaine.github.io/parser/doc/html/boost/parser/stream_error_handler.html  
  
I would expect its constructor to take a reference to an `ostream` object, but anything it takes consistently is the argument of type `string_view`.   
  
Why do I need to provide a file name? Is it the name of the file that represents the output stream? Or is it supposed to be the name of the file that is concatenated into every error message? This needs to be documented.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-27 22:06:58 UTC  
> Url: https://github.com/boostorg/parser/issues/81#issuecomment-1913349655  

That's just the reference doc, but it does state that it takes ostreams, and that if you don't supply them it uses std::cerr.  It also tells you that the name of the string_view is "filename".  I'm not sure what more you want.  
  
Anyway, I think the use of filename is pretty well explained in the tutorial:  
  
https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/error_handling_and_debugging.html
