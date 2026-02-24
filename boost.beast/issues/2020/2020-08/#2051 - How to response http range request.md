# #2051 - How to response http range request [Closed]

> Username: bigvzhang  
> Created at: 2020-08-13 03:32:03 UTC  
> Updated at: 2020-08-24 11:19:05 UTC  
> Closed at: 2020-08-24 11:19:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2051  

### Version of Beast: 189  
  
My server needs to support HTTP range request(as  
https://developer.mozilla.org/en-US/docs/Web/HTTP/Range_requests). But I  
don't know how to implement it.  Could you help me to give me a demo?  
  
  
### Response comparisons  
```  
// For beast  
$ curl -I http://localhost:8090/BOOKS/mp3/NPR/200605/a001.mp3  
HTTP/1.1 200 OK  
Server: Boost.Beast/189  
Content-Type: application/text  
Content-Length: 4559516  
Connection: close  
  
// For nodejs running koa/koa-static/koa-range  
$ curl -I http://localhost:8091/BOOKS/mp3/NPR/200605/a001.mp3  
HTTP/1.1 200 OK  
Accept-Ranges: bytes  
Content-Length: 4559516  
Last-Modified: Fri, 25 May 2007 06:04:37 GMT  
Cache-Control: max-age=0  
Content-Type: audio/mpeg  
Date: Thu, 13 Aug 2020 00:54:15 GMT  
Connection: keep-alive  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-13 05:57:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-673278404  

You can check the request for the Accept-Ranges header, perform your range calculations and set the appropriate status can headers in the response:  
  
```  
HTTP/1.1 206 Partial Content  
Content-Range: bytes 0-1023/146515  
Content-Length: 1024  
...  
(binary content)  
```

---

## Comment 2

> Username: bigvzhang  
> Created at: 2020-08-14 16:39:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-674160588  

Thanks. Working on it. Is there a function to parse range request? If not, is there a function to get range string?  Please help me to improve the following code:  
  
```  
	std::uint64_t  req_range1 = _UI64_MAX;   
	std::uint64_t  req_range2 = _UI64_MAX;  
  
	{  
		static std::regex rgx("^Range:\\s*bytes=(\\d+)-(\\d+){0,1}\\s*$");  
		std::ostringstream oss; oss << req; std::string request_as_string = oss.str();  
		std::cmatch mr;  
		if(std::regex_search(request_as_string.c_str(), mr, rgx)){  
			req_range1 = atoi(mr[1].str().c_str()); // TODO: implement "a to uint64"  
			if(mr[2].matched)  
				req_range2 = atoi(mr[2].str().c_str());  
			//printf("==***Range request from %s to %s\n", mr[1].str().c_str(), mr[2].str().c_str());  
		}  
	}  
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-08-14 16:46:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-674163512  

Hi @bigvzhang   
  
If you can let me have a link to your git repo I’d be happy to offer suggestions or even submit a PR to help out.

---

## Comment 4

> Username: bigvzhang  
> Created at: 2020-08-14 16:57:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-674168385  

Great.  Because my current code is a big mess,  I'll take some time to create a project.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-08-14 16:58:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-674168927  

Don’t be proud :) most projects are a big mess.

---

## Comment 6

> Username: bigvzhang  
> Created at: 2020-08-20 08:35:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-677459934  

The repository is at https://github.com/bigvzhang/vhttp.    
  
The file range_file_body.hpp is based on basic_file_body.hpp to support range request. So far, it only supports one part range request. Lines 184~285 in main.cpp, which is based on advanced_server example, are used to parse range request.  The issue is  that req[http::field::content_range] doesn't work and the bad solution to re-scan the whole request content is used.

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-08-20 13:49:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-677675860  

Hi @bigvzhang , Sorry I've been busy the last few days. Did you figure this out or would you still like my help?

---

## Comment 8

> Username: bigvzhang  
> Created at: 2020-08-20 17:18:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-677793832  

Thanks for your precious time.  Just look into the issue:  
```  
auto sv_range = req[http::field::content_range];  // BAD  
auto cookie = req[http::field::cookie];                   // OK  
```  
Is there an equivalent function to get the range value?

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-08-24 08:50:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-678997695  

When you say "bad" what do you mean? A compilation error?  
  
The field is defined here:  
https://github.com/boostorg/beast/blob/201105e66ab738bd8e027cff8c11ddd7cd5c7587/include/boost/beast/http/field.hpp#L97  
  
and tested here:  
https://github.com/boostorg/beast/blob/201105e66ab738bd8e027cff8c11ddd7cd5c7587/test/beast/http/field.cpp#L109

---

## Comment 10

> Username: bigvzhang  
> Created at: 2020-08-24 11:12:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-679064814  

Thanks for your precious help. I got the problem, field::range should be used because the browser use "Range" but not "Content-Range" to send the request.

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-08-24 11:16:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-679066392  

Great news!  
:clap:

---

## Comment 12

> Username: bigvzhang  
> Created at: 2020-08-24 11:19:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2051#issuecomment-679067318  

I appreciate your help. Have a good day!
