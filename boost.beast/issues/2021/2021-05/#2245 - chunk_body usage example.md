# #2245 - chunk_body usage example [Closed]

> Username: Moerten  
> Created at: 2021-05-26 08:07:03 UTC  
> Updated at: 2021-05-28 09:27:16 UTC  
> Closed at: 2021-05-28 09:27:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2245  

Hi,  
  
could you please provide a small example how too use a HTTP Socket with a "chunk_body"?  
I am receiving a HTTP response with chunked binary data in the body field.  
My code is looking something like the following:  
  
```  
http::response<http::chunk_body<Byte>> response;  
http::async_read(  
		*m_pHttpSocket,  
		m_bufHttpSocket,  
		response,  
		std::bind(  
			&base::OnRead,  
			this,  
			std::placeholders::_1,  
			std::placeholders::_2));  
```  
  
Giving me  
  
```error C2338: BodyReader type requirements not met```  
  
What does this mean?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-26 08:47:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2245#issuecomment-848589573  

Hi @Moerten ,   
  
The approach taken will differ slightly depending on whether you want to treat each chunk incrementally or whether you want to just treat the entire message as one payload that happens to be chunk-encoded.  
  
In the former case you would derive your parser from `http::parser` and override the on_chunk_... methods.  
  
In the latter case you would simply use a standard parser.  
  
Can you let me know what you are looking to achieve so I can guide you?

---

## Comment 2

> Username: Moerten  
> Created at: 2021-05-26 09:42:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2245#issuecomment-848627710  

Hi, thanks for your quick response. I want to process the binary data of the http response. For that I need the whole data. So the standard parser way should be the one to go I think.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-26 10:14:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2245#issuecomment-848648291  

In that case I don't think you need to worry about whether the response it chunked or not. Just use a `string_body`.

---

## Comment 4

> Username: Moerten  
> Created at: 2021-05-26 13:24:08 UTC  
> Updated at: 2021-05-26 14:20:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2245#issuecomment-848767042  

I don't think this works because the binary data obviously contains some not printable signs. If I print the http header on the console the binary part becomes cut off after a few signs. Also it is not possible to copy the binary data in a webbrowser window.   
  
```  
        http::response<http::string_body> response;  
  
	http::async_read(  
		*m_pHttpSocket,  
		m_bufHttpSocket,  
		response,  
		std::bind(  
			&base::OnRead,  
			this,  
			std::placeholders::_1,  
			std::placeholders::_2));  
```  
Tried it with m_bufHttpSocket being both an multi- or flat_buffer.  
  
Update: The body of the string_body response is empty. Although I have received more then 4700 Bytes.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-05-26 14:31:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2245#issuecomment-848819725  

strings can handle binary data perfectly fine. But if you try to print it to the console, it obviously might not print correctly especially if there's a null. You need to either convert the binary data to hexadecimal for printing, or output unprintable characters as escapes. Alternatively, you can simply look at the string using a debugger, which should show you the correct data.

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-05-26 16:24:15 UTC  
> Updated at: 2021-05-26 16:30:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2245#issuecomment-848914906  

```  
#include <boost/algorithm/hex.hpp>  
#include <boost/beast/http.hpp>  
#include <algorithm>  
#include <iostream>  
#include <numeric>  
  
namespace http = boost::beast::http;  
  
void test(http::response<http::string_body> const& resp)  
{  
    boost::algorithm::hex(resp.body(),   
        std::ostream_iterator<char>(std::cout));  
    std::cout << "\n";  
}  
  
int main()  
{  
    http::response<http::string_body> resp;  
  
    std::generate_n(back_inserter(resp.body()),   
        127,   
        [c = char(0)]() mutable { return c++; });  
  
    test(resp);  
}  
```  
  
expected output:  
```  
000102030405060708090A0B0C0D0E0F101112131415161718191A1B1C1D1E1F202122232425262728292A2B2C2D2E2F303132333435363738393A3B3C3D3E3F404142434445464748494A4B4C4D4E4F505152535455565758595A5B5C5D5E5F606162636465666768696A6B6C6D6E6F707172737475767778797A7B7C7D7E  
```

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-05-26 17:29:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2245#issuecomment-848971992  

@madmongo1 An otherwise great example, ruined by GitHub's lack of word wrap >.<

---

## Comment 8

> Username: Moerten  
> Created at: 2021-05-28 09:27:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2245#issuecomment-850284106  

> strings can handle binary data perfectly fine  
  
Didn't expect this. But works like a charm now.
