# #469 - dynamic_body<flat_buffer> size limit ignored by http::async_read() [Closed]

> Username: hoditohod  
> Created at: 2017-06-11 17:25:21 UTC  
> Updated at: 2017-06-12 02:47:42 UTC  
> Closed at: 2017-06-12 02:47:42 UTC  
> Url: https://github.com/boostorg/beast/issues/469  

I would like to have a size limit on the body of the incoming http request to avoid blowing up the server memory with a malicious request.    
  
I created a request<dynamic_body<flat_buffer>> request with the size limit passed in the constructor (as suggested on gitter), this request is passed to async_read(). When I send an oversize request to the server the async completion handler is invoked without any error with the oversize request in the body.     
I would expect a buffer overflow error.  
  
Setting a watchpoint on flat_buffer::_max shows that the limit is correct before the async completion handler is invoked, and the buffer gets overwritten (move constructed) during the http parsing with a default constructed value (std::size_t max) around here:  
  
<pre>  
// read.ipp:410  
        if(d.p.is_done())  
        {  
            d.m = d.p.release();  
            goto upcall;  
        }  
  
</pre>  
Beast version: 52  
Compiler GCC 5.4.0 Ubuntu 16.04 x86_64

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-11 17:47:41 UTC  
> Url: https://github.com/boostorg/beast/issues/469#issuecomment-307645107  

Thank you for the detailed report. I believe **v52** has this in it:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/dynamic_body.hpp#L115  
  
So the error should be generated and detected here:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/read.ipp#L141  
  
I recently made fixes to `flat_buffer`, its possible that the move assignment or move constructor of `flat_buffer` has/had a bug where it didn't transfer the `max_` setting. Investigating...

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-11 17:58:46 UTC  
> Url: https://github.com/boostorg/beast/issues/469#issuecomment-307645747  

I see what's going on. You are calling `async_read` which takes the `message` as a parameter. The implementation constructs a parser for you and then assigns the result to your variable. When this happens, it of course overwrites your limit. Here's the implementation of `read`:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/read.ipp#L728  
  
Note how the parser is default constructed, which means that the body is also default constructed. That line, for you, should read:  
```  
parser<true, dynamic_body<flat_buffer>, fields> p{limit};  
```  
  
Where `limit` is your limit. Arguments passed to the parser constructor are forwarded to the message it contains. I provided the simple `read` functions which take the message as a parameter as a convenience for example code and for users to get comfortable with the library. You have discovered a limitation.  
  
To make your use-case work, do not pass a message to `read`. Instead, construct a `parser` yourself, with appropriate constructor arguments such as the max, and call the overload of `read` which takes the parser. You have to manage the lifetime of the parser, the same way you are managing the lifetime of the message. When you're done call `get()` on the parser to retrieve your message. Or, call `release()` to take ownership.  
  
I will rethink this "simple" interface in light of your problem.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-11 18:09:23 UTC  
> Url: https://github.com/boostorg/beast/issues/469#issuecomment-307646387  

I think the read overloads which take a `message&` parameter should be removed entirely. This will make the examples a little harder to read, but also prevent anyone from encountering this issue.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-11 18:50:51 UTC  
> Url: https://github.com/boostorg/beast/issues/469#issuecomment-307648797  

This was an easy fix... LOL

---

## Comment 5

> Username: hoditohod  
> Created at: 2017-06-11 19:53:24 UTC  
> Url: https://github.com/boostorg/beast/issues/469#issuecomment-307652520  

Works perfectly, thanks!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-11 20:04:05 UTC  
> Url: https://github.com/boostorg/beast/issues/469#issuecomment-307653226  

It hasn't been merged to master yet though, there's still time to break it :) This issue will automatically close itself when the code is merged.
