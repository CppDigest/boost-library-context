# #1137 - Question : How to read large file (get request) to memory [Closed]

> Username: Millieen  
> Created at: 2018-05-21 07:12:02 UTC  
> Updated at: 2018-05-21 08:29:48 UTC  
> Closed at: 2018-05-21 08:29:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1137  

### Version of Beast  BOOST 1.66.0  
  
I want to send a http get request to read a large file data. With searching many solutions, finally I got this (I use http_client_sync.cpp):  
  
	http::response_parser<http::file_body> res;  
	res.body_limit(0xffffffffffffffff);  
	res.get().body().open(localfilename, boost::beast::file_mode::write, ec);  
	int readlen = http::read(socket, buffer, res, ec);  
  
It works, but I don't want to save the data to file, only want to keep the buffer in memory.  
I don't know how to do. It seems there is no similar demos either, is there anyone can help?  
  
Thank you very much!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-21 07:46:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1137#issuecomment-390579245  

You want to read the full response into memory? Use a string body:  
```  
http::response_parser<http::string_body> res;  
```

---

## Comment 2

> Username: Millieen  
> Created at: 2018-05-21 08:29:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1137#issuecomment-390588794  

> You want to read the full response into memory? Use a string body:  
> http::response_parser<http::string_body> res;  
   
It works, Thanks!
