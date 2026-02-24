# #183 - Extending beast::http::request [Closed]

> Username: avalchev  
> Created at: 2016-11-11 17:29:52 UTC  
> Updated at: 2016-11-11 18:27:14 UTC  
> Closed at: 2016-11-11 18:27:14 UTC  
> Url: https://github.com/boostorg/beast/issues/183  

I need additional information to be added to request, such as cookies or request timestamp.  
  
Is extending `beast::http::request` class is a good practice or composition must be used ?  
  
For example:  
```  
class Request: public beast::http::request<beast::http::streambuf_body>  
{  
public:  
  
  Request() {}  
  
  virtual ~Request() {}  
  
private:  
  
  std::map<std::string, std::string> _cookies;  
  
};  
```  
  
Thanks :)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-11 18:20:45 UTC  
> Url: https://github.com/boostorg/beast/issues/183#issuecomment-260021156  

If you're asking whether you should derive from `beast::http::message`, or have `beast::http::message` as a data member in your class, I would suggest the data member (i.e. composition).

---

## Comment 2

> Username: avalchev  
> Created at: 2016-11-11 18:27:14 UTC  
> Url: https://github.com/boostorg/beast/issues/183#issuecomment-260022513  

Thanks :)
