# #1771 - Server redirect example [Closed]

> Username: benstadin  
> Created at: 2019-11-25 23:13:48 UTC  
> Updated at: 2019-11-26 02:07:14 UTC  
> Closed at: 2019-11-26 02:02:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1771  

I'm serving a simple static page which implicitely loads an index.html when the base url is entered in the browser (/appresource/a will return ./appresource/a/index.html). I'm looking for a server side redirect example, because obviously the relative files referred within index.html will have the wrong request target (linked image.png in HTML will have target ./appresource/image.png instead of ./appresource/a/image.png).  
  
What's the best way to do a server side redirect?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-26 01:45:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1771#issuecomment-558420809  

> What's the best way to do a server side redirect?  
  
Build the HTTP response with the correct header and body, and write it?

---

## Comment 2

> Username: benstadin  
> Created at: 2019-11-26 02:02:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1771#issuecomment-558424581  

Sorry, it was obvious in the end.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-26 02:07:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1771#issuecomment-558425546  

Often, simply asking the question answers it.
