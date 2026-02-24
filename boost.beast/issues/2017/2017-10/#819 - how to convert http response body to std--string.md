# #819 - how to convert http response body to std::string? [Closed]

> Username: lstrcat  
> Created at: 2017-10-19 08:38:08 UTC  
> Updated at: 2018-09-05 11:40:29 UTC  
> Closed at: 2017-10-23 01:28:18 UTC  
> Url: https://github.com/boostorg/beast/issues/819  

`  
       http::response <http::dynamic_body> res;  
  
        // Receive the HTTP response  
        http::read(stream, buffer, res);  
  
        // Write the message to standard out  
        std::cout << res << std::endl;  
          
          
        std::string s = res.body.data(); 	// error here!  
  
i really dont understand the **Buffer Types** in beast ! orz  
help me please

---

## Comment 1

> Username: arun11299  
> Created at: 2017-10-19 09:27:38 UTC  
> Updated at: 2017-10-19 09:28:00 UTC  
> Url: https://github.com/boostorg/beast/issues/819#issuecomment-337852222  

You can use `std::string s = boost::asio::buffer_cast<const char*>(res.body.data());`   
  
If the body is of something like `multi_buffer`, the you need to iterate through the buffer sequence and append it to the string, something like:  
  
```  
for (auto seq : buf_sequence) {  
  auto* cbuf = boost::asio::buffer_cast<const char*>(seq);  
  s.append(cbuf, boost::asio::buffer_size(seq));  
}   
```

---

## Comment 2

> Username: lstrcat  
> Created at: 2017-10-19 09:45:31 UTC  
> Url: https://github.com/boostorg/beast/issues/819#issuecomment-337856769  

Thank you!  
 the **dynamic_body** is **multi_buffer** , how to decide to use **string_body** or **dynamic_body**?

---

## Comment 3

> Username: arun11299  
> Created at: 2017-10-19 10:05:14 UTC  
> Url: https://github.com/boostorg/beast/issues/819#issuecomment-337861796  

Well, that depends upon what kind of performance characteristics you are looking for. In layman terms,  `string_body` would have all the data in a single heap block. So as and when you get more streamed data, more reallocations (and copy) would happen. `multi_buffer` is more like a linked list of allocated pools.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-10-19 14:21:54 UTC  
> Updated at: 2017-10-19 15:28:09 UTC  
> Url: https://github.com/boostorg/beast/issues/819#issuecomment-337923558  

You have a few choices here.  
  
First, you can use `buffers_to_string` like this:  
```  
std::cout << boost::beast::buffers_to_string(res.body.data()) << std::endl;  
```  
https://github.com/boostorg/beast/blob/885b9dfe0b6bfc7be6a9158d60f0760aa43e748a/include/boost/beast/core/buffers_to_string.hpp#L42  
  
Or you can declare the message to use a string body:  
```  
using namespace boost::beast;  
http::response<http::string_body> res;  
```

---

## Comment 5

> Username: lstrcat  
> Created at: 2017-10-23 01:28:18 UTC  
> Url: https://github.com/boostorg/beast/issues/819#issuecomment-338525519  

Thanks for your help:)

---

## Comment 6

> Username: lls2wow  
> Created at: 2017-11-16 23:17:37 UTC  
> Url: https://github.com/boostorg/beast/issues/819#issuecomment-345094260  

@vinniefalco  I can`t find the   file beast/include/boost/beast/core/buffers_to_string.hpp in   beast include path.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-11-16 23:31:42 UTC  
> Url: https://github.com/boostorg/beast/issues/819#issuecomment-345097132  

@lls2wow Which version of Beast?

---

## Comment 8

> Username: lls2wow  
> Created at: 2017-11-17 20:42:08 UTC  
> Url: https://github.com/boostorg/beast/issues/819#issuecomment-345359931  

v84-1

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-11-17 21:22:13 UTC  
> Url: https://github.com/boostorg/beast/issues/819#issuecomment-345368674  

I suggest using **version 124**, available here:  
https://github.com/boostorg/beast/tree/v124  
  
Or, wait until Boost 1.66.0 is released (in a few weeks) and use that.

---

## Comment 10

> Username: harajyotidas  
> Created at: 2018-09-05 11:40:29 UTC  
> Url: https://github.com/boostorg/beast/issues/819#issuecomment-418699876  

> You have a few choices here.  
>   
> First, you can use buffers_to_string like this:  
>   
> std::cout << boost::beast::buffers_to_string(res.body.data()) << std::endl;  
  
  
Just a minute correction. It should be  
  
`std::cout << boost::beast::buffers_to_string(res.body().data()) << std::endl;`
