# #2616 - Parsing Headers and Getting Cookies [Closed]

> Username: WarrenN1  
> Created at: 2023-01-06 11:05:00 UTC  
> Updated at: 2023-08-18 13:48:20 UTC  
> Closed at: 2023-08-18 13:48:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2616  

How do I access the headers and get the cookies from an incoming HTTP:Request? So far I am aware that the following can be done:  
```  
for(auto& h : req.base())  
{  
    std::cerr << "Header: "<< h.name()<< ", Header Name: " << h.name_string() << ", Value: " << std::string(h.value()) << "\n";  
```  
But for some headers like:  
Content-Type  
```  
Content-Type: multipart/form-data; boundary=something  
```  
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type  
  
There does not seem to be a built in way to access directives like "boundary=something"  
It seems that most headers lack support by beast, but I am wondering if I missed something in the docs?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-01-06 11:59:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2616#issuecomment-1373541110  

No, it's not supported.
