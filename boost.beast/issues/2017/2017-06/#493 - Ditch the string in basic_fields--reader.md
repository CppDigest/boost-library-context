# #493 - Ditch the string in basic_fields::reader [Closed]

> Username: vinniefalco  
> Created at: 2017-06-15 05:46:04 UTC  
> Updated at: 2017-06-15 19:49:21 UTC  
> Closed at: 2017-06-15 19:49:21 UTC  
> Url: https://github.com/boostorg/beast/issues/493  

There are still a leftover `std::string` in the `reader` implementation:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/fields.ipp#L136  
  
Once this is gone, the serializer will be truly zero-alloc.  
  
We could just put a reasonable upper limit on the start-line and go with a `static_string`
