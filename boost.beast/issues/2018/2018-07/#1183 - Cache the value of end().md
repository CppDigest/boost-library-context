# #1183 - Cache the value of end() [Closed]

> Username: vinniefalco  
> Created at: 2018-07-11 02:31:25 UTC  
> Updated at: 2022-10-05 15:15:31 UTC  
> Closed at: 2022-10-05 15:15:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1183  

There are a few loops which retrieve the value of `end()` for each iteration in the loop (sometimes twice, see fields.ipp `get_chunked_impl()`). This provides guidance:|  
http://llvm.org/docs/CodingStandards.html#don-t-evaluate-end-every-time-through-a-loop
