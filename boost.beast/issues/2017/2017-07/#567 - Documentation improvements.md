# #567 - Documentation improvements [Closed]

> Username: vinniefalco  
> Created at: 2017-07-02 00:03:50 UTC  
> Updated at: 2017-07-03 21:51:55 UTC  
> Closed at: 2017-07-03 21:51:55 UTC  
> Url: https://github.com/boostorg/beast/issues/567  

A reviewer writes:  
  
> * I found the mix of function and type entries in "Table 6. Buffer Algorithms" to be initially quite confusing.  Maybe separate them?  
  
How about changing the title to "Buffer Algorithms and Types"  
  
> * The buffer_prefix_view documentation in "Table 6. Buffer Algorithms" says "This is the type of buffer returned by buffer_prefix.", but 2/3 overloads with that name return something else.  
  
`buffer_prefix` should be a link to the correct overload.  
  
> "This function can manipulate the fields common to requests and responses. If it needs to access the other fields, it can do so using tag dispatch with an object of type std::integral_constant<bool, isRequest>" Why would I have a third overload and use tag dispatching?  Most users are going to be really confused by what I quoted above, I think.  
  
This is from the Design->Message Container section of the doc, the wording could be better.
