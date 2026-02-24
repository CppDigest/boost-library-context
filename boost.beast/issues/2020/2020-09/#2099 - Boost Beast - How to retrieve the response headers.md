# #2099 - Boost Beast | How to retrieve the response headers ? [Closed]

> Username: gdias1992  
> Created at: 2020-09-23 04:49:18 UTC  
> Updated at: 2020-09-23 05:55:25 UTC  
> Closed at: 2020-09-23 05:24:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2099  

Can someone help me retrieve the response headers in the example below ?  
  
https://pastebin.com/STYUtAyw  
  
Thanks all :D  
  
P.S.: Take easy with me, i am starting now at c++ and would love a "for dummies" answer with a sample code.

---

## Comment 1

> Username: gdias1992  
> Created at: 2020-09-23 05:24:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2099#issuecomment-697141227  

Just found out the solution.  
```  
for (auto& header : res.base())			  
    std::cout << "Field: " << header.name() << " /text: " << header.name_string() << ", Value: " << header.value() << "\n";			  
}  
```  
  
Outputs:  
  
```  
Field: Age /text: Age, Value: 437767  
Field: Cache-Control /text: Cache-Control, Value: max-age=604800  
Field: Content-Type /text: Content-Type, Value: text/html; charset=UTF-8  
Field: Date /text: Date, Value: Tue, 23 Jun 2020 16:43:43 GMT  
Field: ETag /text: Etag, Value: "3147526947+ident"  
Field: Expires /text: Expires, Value: Tue, 30 Jun 2020 16:43:43 GMT  
Field: Last-Modified /text: Last-Modified, Value: Thu, 17 Oct 2019 07:18:26 GMT  
Field: Server /text: Server, Value: ECS (bsa/EB15)  
Field: Vary /text: Vary, Value: Accept-Encoding  
Field: <unknown-field> /text: X-Cache, Value: HIT  
Field: Content-Length /text: Content-Length, Value: 1256  
```

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-09-23 05:55:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2099#issuecomment-697150042  

Great news.  
  
If you need any more help, we hang out on the cpplang slack server, channel #beast  
  
https://cpplang-inviter.cppalliance.org/
