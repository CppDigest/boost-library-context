# #621 - message::base() needs better docs [Open]

> Username: vinniefalco  
> Created at: 2017-07-08 00:36:18 UTC  
> Updated at: 2018-12-21 15:08:50 UTC  
> Url: https://github.com/boostorg/beast/issues/621  

There should be an example of use:  
```  
request<string_body> req;  
...  
std::cout << req.base(); // print just the header portion  
```  
  
Also we should group the overloads with **defgroup** or something so they don't require the overload page.

---

## Comment 1

> Username: jaredwy  
> Created at: 2017-07-08 00:38:52 UTC  
> Url: https://github.com/boostorg/beast/issues/621#issuecomment-313822113  

As per our conversation on slack. It would also be great to show an example of req[http::fields::*] as well.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-08 00:48:36 UTC  
> Url: https://github.com/boostorg/beast/issues/621#issuecomment-313822762  

That could go in the description for `message` but keep in mind, that syntax only works when you are using `basic_fields` (the default **Fields** type).
