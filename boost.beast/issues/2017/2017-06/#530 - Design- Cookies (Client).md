# #530 - Design: Cookies (Client) [Closed]

> Username: vinniefalco  
> Created at: 2017-06-22 20:51:13 UTC  
> Updated at: 2022-03-16 23:32:00 UTC  
> Closed at: 2017-07-04 01:50:59 UTC  
> Url: https://github.com/boostorg/beast/issues/530  

The question is raised on what an API might look like for handling client cookies. Here it is  
```  
/** Stores cookies.  
  
    This class stores cookies acquired in HTTP responses or persisted,  
    and fills in cookie fields for outgoing HTTP requests.  
*/  
template<class Derived>  
class cookie_jar  
{  
public:  
    /** Extract cookies from an HTTP response.  
  
        This function scans the fields in the response and updates  
        stored cookies according to the Set-Cookie fields.  
  
        @param res The response to scan.  
    */  
    template<class Body, class Fields>  
    void  
    extract(response<Body, Fields> const& res);  
  
    /** Add stored cookies to an HTTP request  
  
        This function adds cookie fields from stored cookies to  
        the specified HTTP request.  
  
        @note Undefined behavior if called twice on the same request object.  
    */  
    template<class Body, class Fields>  
    void  
    add(request<Body, Fields>& req);  
};  
```  
  
This interface uses CRTP (Curiously Recurring Template Pattern) where the derived class provides behaviors like saving to disk or filtering values.

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-06-22 21:01:34 UTC  
> Updated at: 2017-06-22 21:07:32 UTC  
> Url: https://github.com/boostorg/beast/issues/530#issuecomment-310501494  

> This interface uses CRTP  
  
It is the base :D Who calls this interface?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-22 23:03:43 UTC  
> Url: https://github.com/boostorg/beast/issues/530#issuecomment-310525934  

Someone who builds a client would call into this interface. Or if we're going low level then anyone who is writing client code in Beast would call into it for every request and response.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-04 01:50:59 UTC  
> Url: https://github.com/boostorg/beast/issues/530#issuecomment-312761521  

This is outside the scope of Beast for now, closing

---

## Comment 4

> Username: kingofoz  
> Created at: 2018-04-08 10:47:21 UTC  
> Url: https://github.com/boostorg/beast/issues/530#issuecomment-379540418  

Looks it is not difficult to implement cookie-jar. Why not write an example for it at least?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-04-08 16:02:56 UTC  
> Url: https://github.com/boostorg/beast/issues/530#issuecomment-379561215  

Okay, well then why don't you write it?

---

## Comment 6

> Username: kingofoz  
> Created at: 2018-04-09 01:56:01 UTC  
> Url: https://github.com/boostorg/beast/issues/530#issuecomment-379604802  

um..I am very new to beast and C++. I think I cannot write it with high quality and I still need much time to learn them.

---

## Comment 7

> Username: luz-arreola  
> Created at: 2022-03-14 21:23:05 UTC  
> Updated at: 2022-03-16 23:32:00 UTC  
> Url: https://github.com/boostorg/beast/issues/530#issuecomment-1067308738  

Hello Vinnie hope you're doing well.  I am obtaining the entire cookie header as follows:  
  
std::string str = req[http::field::cookie].to_string();  
  
I then parse the string myself since there does not seem to be a cookie parser in this library.  Is this a good approach or is there a built in parsing technique that I'm unaware of?  Thank you.  
  
Update: I ended up creating a class to parse, index as well as to check that cookie contents comply with RFC 6265.  Was not fun but at least I know what's going on which is always a good thing.
