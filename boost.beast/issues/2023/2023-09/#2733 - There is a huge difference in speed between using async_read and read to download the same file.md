# #2733 - There is a huge difference in speed between using async_read and read to download the same file [Closed]

> Username: zhllxt  
> Created at: 2023-09-01 11:43:14 UTC  
> Updated at: 2023-09-01 15:24:24 UTC  
> Closed at: 2023-09-01 12:28:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2733  

```cpp  
std::fstream file(strSavePath, std::ios::out | std::ios::binary | std::ios::app);  
http::response_parser<http::buffer_body> parser(std::move(res));  
const int nBufSize = 512;  
char* buf = new char[nBufSize];  
while (!parser.is_done())  
{  
	parser.get().body().data = buf;  
	parser.get().body().size = nBufSize;  
  
	/* 1 */std::size_t nRead = http::read(stream, buffer, parser, ec);  
	/* 2 */std::size_t nRead = http::async_read(stream, buffer, parser, yield[ec]);  
	if (ec)  
	{  
		if (ec == http::error::need_buffer)  
		{  
			ec = {};  
		}  
		else  
		{  
			fail(ec, "read");  
			break;  
		}  
	}  
  
	//...  
}  
```  
when use /* 1 */, the download speed is 70MB/s  
when use /* 2 */, the download speed is 8MB/s,   
why ?  
All other codes are the same.

---

## Comment 1

> Username: ashtum  
> Created at: 2023-09-01 11:56:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2733#issuecomment-1702631555  

@zhllxt are you running a release or debug build? `async_read` goes through lots of indirection for each operation to complete additionally it causes the `stackful` coroutine to pause and resume on each operation. so in debug builds it would be marginally slower.

---

## Comment 2

> Username: zhllxt  
> Created at: 2023-09-01 12:27:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2733#issuecomment-1702667632  

> @zhllxt are you running a release or debug build? `async_read` goes through lots of indirection for each operation to complete additionally it causes the `stackful` coroutine to pause and resume on each operation. so in debug builds it would be marginally slower.  
  
yes, i was running a debug build.  
  
after test on release mode, this issue has disappeared.  
  
Thank You!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-09-01 15:24:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2733#issuecomment-1702928031  

LOL... :) :) :)
