# #1765 - How to get the full chunked data from http response in synchronous mode? [Closed]

> Username: zaley2000  
> Created at: 2019-11-13 07:51:12 UTC  
> Updated at: 2019-12-21 03:30:29 UTC  
> Closed at: 2019-12-21 02:31:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1765  

In synchronous mode ,I want to get the full chunked data from http response . Is there a related example? My boost  version is 1.71.  
  
      .......  
       beast::http::write(stream, req);  
       beast::flat_buffer buffer;  
       beast::http::response<beast::http::dynamic_body> res;					  
	int rt = beast::http::read(stream, buffer, res);  
       bool chunked = res.chunked();  
	if (chunked)  
	{  
          // ?????  
       }

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-13 14:32:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1765#issuecomment-553429337  

Beast removes any chunked-encoding for you. Your code above will read the complete body into the message. I would suggest you use `beast::http::string_body` instead of `dynamic_body`, it will be easier for you to access the contents.

---

## Comment 2

> Username: zaley2000  
> Created at: 2019-11-14 01:04:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1765#issuecomment-553676161  

Oh, I have get the chunked data like this  
  
	beast::flat_buffer buffer;  
	beast::http::response<beast::http::dynamic_body> res;					   
	int rt = beast::http::read(stream, buffer, res);  
	bool chunked = res.chunked();  
	int nS = res.body().size();  
	auto bd = res.body().data();  
     //std::string line = buffers_to_string( bd) ;  
	for (auto const buffer : beast::buffers_range_ref(bd))  
		method->responseBody.append(static_cast<char const*>(buffer.data()), buffer.size());

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-14 01:24:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1765#issuecomment-553680582  

"chunked" has nothing to do with that. A `dynamic_body` uses the `dynamic_buffer` container, which is similar to a deque. It can have more than one buffer. If you instead use `http::string_body`, all your data will be in one buffer and you will not need to make a copy, as you are doing.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-12-14 01:49:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1765#issuecomment-565669461  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-12-21 02:31:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1765#issuecomment-568144630  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-12-21 03:30:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1765#issuecomment-568148347  

This will fix itself with Boost.URL
