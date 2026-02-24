# #596 - How to access the values of headers in HTTP reponse? [Closed]

> Username: karengabrielyan  
> Created at: 2017-07-04 21:15:39 UTC  
> Updated at: 2017-07-04 22:38:49 UTC  
> Closed at: 2017-07-04 21:47:45 UTC  
> Url: https://github.com/boostorg/beast/issues/596  

Hi,  
  
I could not figure out from documentation and code how one can access the values of the headers that come back with HTTP reponse.  
  
Can you explain?  
  
If I have the variable below, how can I access let's say the Location header that comes back as part of HTTP 302 redirect response?  
      `beast::http::response<beast::http::dynamic_body> httpResponse;`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-04 21:20:44 UTC  
> Url: https://github.com/boostorg/beast/issues/596#issuecomment-312956849  

These two statements are equivalent:  
```  
std::cout << "Location: " << httpResponse[beast::http::field::location] << std::endl;  
std::cout << "Location: " << httpResponse["Location"] << std::endl;  
```  
  
Hope this helps!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-04 21:21:35 UTC  
> Url: https://github.com/boostorg/beast/issues/596#issuecomment-312956934  

The `beast::http::message` inherits all of the members from the **Fields** it is derived from. So for `beast::http::response` (or `beast::http::request`) using the default fields type, you have all these members:  
http://vinniefalco.github.io/beast/beast/ref/beast__http__basic_fields.html  
  
Thanks

---

## Comment 3

> Username: karengabrielyan  
> Created at: 2017-07-04 21:47:33 UTC  
> Url: https://github.com/boostorg/beast/issues/596#issuecomment-312959233  

Thanks, works very well!  
  
Might just be worth to add a new passage to the Usage section on this page explaining the headers access, to help with the Boost review.  
http://vinniefalco.github.io/beast/beast/using_http/message_containers.html  
  
Something in line with:  
  
> Access headers and content of the Repsonse  
> ...  
  
Awesome library, thank you!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-04 22:38:49 UTC  
> Url: https://github.com/boostorg/beast/issues/596#issuecomment-312963082  

Good idea!
