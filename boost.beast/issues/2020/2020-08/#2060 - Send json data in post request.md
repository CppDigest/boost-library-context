# #2060 - Send json data in post request [Closed]

> Username: a1987zz  
> Created at: 2020-08-23 10:19:26 UTC  
> Updated at: 2020-08-30 18:06:12 UTC  
> Closed at: 2020-08-30 18:06:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2060  

How to send a post the request figured out. And how can you transfer json data in a post request. Thank. Json request is simple:  
`  
{  
  "content": "*reads manual*"  
}  
`

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-23 13:14:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2060#issuecomment-678773198  

Set the content_type field to “application/json” and put the serialised JSON text into the body. Then POST your request. That’s it.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-23 13:52:59 UTC  
> Updated at: 2020-08-23 13:53:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2060#issuecomment-678777212  

```  
...  
req.set_field( http::field::content_type, "application/json" );  
req.body() = "{ \"content\": \"*reads manual*\" }";  
req.prepare_payload();  
```

---

## Comment 3

> Username: a1987zz  
> Created at: 2020-08-30 17:23:59 UTC  
> Updated at: 2020-08-30 17:25:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2060#issuecomment-683446980  

Thanks it works.
