# #1372 - How to read response body of type http::file_body [Closed]

> Username: NipunRamani99  
> Created at: 2018-12-16 19:39:10 UTC  
> Updated at: 2018-12-18 21:13:23 UTC  
> Closed at: 2018-12-18 21:13:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1372  

Hey,  
sorry if this has been asked before, but is there a way to read the contents of body in this example:  
```cpp  
      	http::response<http::file_body> res;  
	boost::system::error_code ec;  
	res.body().open("public\\index.html",beast::file_mode::scan,ec);  
	res.prepare_payload();  
```  
Edit:  
Also is there a convention that should be followed while passing a path to a file on the server?  
If I give the path as /public/index.html response object is unable to open index.html. I am using beast on Windows.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-16 20:26:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1372#issuecomment-447673081  

Call `http::read` with the appropriate `parser`

---

## Comment 2

> Username: NipunRamani99  
> Created at: 2018-12-17 07:46:39 UTC  
> Updated at: 2018-12-17 07:48:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1372#issuecomment-447751422  

I am confused about using http::read since I am not reading the data from the socket. I am trying to look into the contents of the response's body after it has read the data from index.html.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-17 15:20:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1372#issuecomment-447882287  

> I am trying to look into the contents of the response's body after it has read the data from index.html.  
  
Beast does not read the entire file into memory when you open it, that would be inefficient. The data from the file is only read in during serialization, and then only a little bit at a time (4KB I believe). If you want to view the contents of the file, you should simply open it and read it yourself.  
  
> is there a convention that should be followed while passing a path to a file on the server  
  
Yes, the path must be a native operating system path.

---

## Comment 4

> Username: NipunRamani99  
> Created at: 2018-12-18 21:13:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1372#issuecomment-448372537  

Alright, so it took a while for me to understand the examples, if we want to receive  data from the other side, we use http::dynamic_body because the type is just going to be string but the size of data will vary.  
I am still a bit confused about parsers and serializers but that's not what this issue is about.  
I'm closing this thread but if I said something wrong just reopen it and post them. Thanks a lot!
