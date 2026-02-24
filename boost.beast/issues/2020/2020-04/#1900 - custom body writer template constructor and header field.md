# #1900 - custom body writer template constructor and header field [Closed]

> Username: AlexandreBossard  
> Created at: 2020-04-12 00:49:30 UTC  
> Updated at: 2021-04-12 17:21:59 UTC  
> Closed at: 2020-04-24 17:37:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1900  

using beast 1.71.  
  
Hi, I'm having a hard time to figure out how I can use/set the header field in my custom body writer. the documentation says that "_the writer shall not access the contents of h or v before the first call to init, permitting lazy construction of the message._ " [source](https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/concepts/BodyWriter.html). But how am i suppose to store the headers since the type is lost outside of the writer's constructor ?  
  
Technically, I would like to set the content_type for my custom body type but I am bit at a loss of how to do it.  
  
This makes me wonder what the constructor parameters are for or what are you supposed to do with it  ?  
```  
class json_writer  
{  
public:  
  using const_buffers_type = net::const_buffer;  
  
  template <bool isRequest, typename Fields>  
  json_writer(http::header<isRequest, Fields> & h, json_wrapper const& b)  
    : body_(b)  
  {  
    // if (h.find(http::field::content_type) == h.end())  
    //   h.set(http::field::content_type, "application/json");  
  }  
// ....  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-04-12 00:58:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1900#issuecomment-612545689  

custom bodies cannot set headers. You need to do this in the calling code.

---

## Comment 2

> Username: AlexandreBossard  
> Created at: 2020-04-12 01:44:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1900#issuecomment-612549040  

Alright then  :/  
  
So, what is the **header** parameter supposed to be used for ?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-04-12 02:10:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1900#issuecomment-612550899  

So you can _read_ the header and know what to do

---

## Comment 4

> Username: terrywh  
> Created at: 2021-04-12 16:23:19 UTC  
> Updated at: 2021-04-12 16:24:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1900#issuecomment-817947320  

documentation says: "The writer shall not access the contents of h or v before the first call to init, permitting lazy construction of the message." but we cant access header int "init" function, so what to do? @vinniefalco can you show us an example?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-04-12 16:35:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1900#issuecomment-817955981  

It might not work as advertised

---

## Comment 6

> Username: terrywh  
> Created at: 2021-04-12 17:06:51 UTC  
> Updated at: 2021-04-12 17:07:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1900#issuecomment-817977213  

> It might not work as advertised  
  
does it mean for now we can't implement a body writer/reader depends on the header?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-04-12 17:21:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1900#issuecomment-817987508  

> does it mean for now we can't implement a body writer/reader depends on the header?  
  
Yes it might not be possible.
