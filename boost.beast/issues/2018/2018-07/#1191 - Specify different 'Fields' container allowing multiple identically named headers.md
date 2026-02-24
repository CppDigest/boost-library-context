# #1191 - Specify different 'Fields' container allowing multiple identically named headers? [Closed]

> Username: carolinebeltran  
> Created at: 2018-07-14 21:30:46 UTC  
> Updated at: 2020-02-28 14:07:04 UTC  
> Closed at: 2018-07-15 00:32:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1191  

### Version of Beast  
  
Beast 167  
  
### Steps necessary to reproduce the problem  
  
http::response<http::string_body> res{ };  
res.set("Set-Cookie", "theme = dark;");  
res.set("Set-Cookie", "token = abc123;");  
  
### All relevant compiler information  
  
No compiler errors.  
  
The problem with my code above is that the default 'Fields' container will only permit you to add unique header names.  This is a problem when trying to set more than one cookie since each distinct cookie requires its very own 'Set-Cookie' header.  
  
Example desired response output:  
  
HTTP/1.0 200 OK  
Set-Cookie: theme = dark;  
Set-Cookie: token = abc123;  
…  
  
Is there an example of how to override the default 'Fields' container with one that will allow multiple identical header names?  Thank you!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-14 21:44:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1191#issuecomment-405051860  

I am certain that `basic_fields` allows the same field to appear more than once, as these tests indicate:  
https://github.com/boostorg/beast/blob/e9e5d159c6f93a6901d8a2e310e6cd1703e38744/test/beast/http/fields.cpp#L369  
  
Also the documentation says as much:  
_"If one or more fields with the same name already exist, the new field will be inserted after the last field with the matching name, in serialization order."_  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields/insert/overload2.html  
  
What does your code look like?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-07-14 21:47:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1191#issuecomment-405052008  

Oops... I see what happened. You are using `basic_fields::set`, which has this behavior:  
_"Set a field value, removing any other instances of that field."_  
  
Try using `basic_fields::insert` instead!

---

## Comment 3

> Username: carolinebeltran  
> Created at: 2018-07-14 23:54:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1191#issuecomment-405057112  

Thank you Vinnie!  What a wonderful library!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-07-15 02:10:35 UTC  
> Updated at: 2018-07-15 13:33:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1191#issuecomment-405062197  

By the way, I think you can instead write:  
```  
http::response<http::string_body> res{ };  
res.set(http::field::set_cookie, "theme = dark;");  
res.set(http::field::set_cookie, "token = abc123;");  
```

---

## Comment 5

> Username: lls2wow  
> Created at: 2018-10-11 11:31:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1191#issuecomment-428920934  

Hi ，@vinniefalco  
  
    Brothers wondered how set multiple same name header. Could you tell me how to parse the same header ?   
```  
	std::stringstream set_cookieSS;  
	set_cookieSS << res[http::field::set_cookie];  
```  
  
My code just parsed the first 'set-cookie' how to get second or third header name "set-cookie"?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-10-11 14:11:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1191#issuecomment-428970523  

Use `equal_range`:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields/equal_range.html

---

## Comment 7

> Username: lls2wow  
> Created at: 2018-10-13 00:13:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1191#issuecomment-429494256  

@vinniefalco  Thank you,Vinnie

---

## Comment 8

> Username: neel  
> Created at: 2020-02-28 14:00:43 UTC  
> Updated at: 2020-02-28 14:07:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1191#issuecomment-592525151  

> By the way, I think you can instead write:  
>   
> ```  
> http::response<http::string_body> res{ };  
> res.set(http::field::set_cookie, "theme = dark;");  
> res.set(http::field::set_cookie, "token = abc123;");  
> ```  
  
Documentation of `set` mentions   
  
>  ... removing any other instances of that field  
  
So In the example you provided the value `theme = dark` will be replaced by `token = abc123`. Is that right ? or there is something special about `Set-Cookie` header ?  
  
The `Set-Cookie` field is required to have multiple instances in the response header. Hence either the http framework or beast should have an exception for this specific field. So for all**?** other fields `set` will be used but for `http::field::set_cookie` it will go through `insert` instead.  
  
I have a function to patch a `boost::beast::http::response` and add the headers from a member variable. This is how it looks.  
  
```cpp  
template<class Body, class Fields>  
void patch(boost::beast::http::message<false, Body, Fields>& res){  
    for(const auto& header: _response_headers){  
        if(header.name() != boost::beast::http::field::set_cookie){  
            res.set(header.name(), header.value());  
        }  
    }  
    res.erase(boost::beast::http::field::set_cookie);  
    for(const auto& header: _response_headers){  
        if(header.name() == boost::beast::http::field::set_cookie){  
            res.insert(header.name(), header.value());  
        }  
    }  
}  
```  
  
I was wondering whether this implementation is okay or it is doing something that `beast` already does ? Is there any other header that is required to have multiple instances ?
