# #108 - make result::error accessible by reference or move [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-03-07 23:05:40 UTC  
> Updated at: 2023-03-18 05:17:27 UTC  
> Closed at: 2023-03-18 05:17:27 UTC  
> Url: https://github.com/boostorg/system/issues/108  

When using result with custom types, the error might be non-trival, but hold on to resources, such a strings. In those cases it would be very useful if the error could be obtained without a copy.
