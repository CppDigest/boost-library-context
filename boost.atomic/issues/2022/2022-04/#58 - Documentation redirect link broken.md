# #58 - Documentation redirect link broken [Closed]

> Username: glenfe  
> Created at: 2022-04-18 12:06:43 UTC  
> Updated at: 2022-04-19 08:17:12 UTC  
> Closed at: 2022-04-18 12:14:21 UTC  
> Url: https://github.com/boostorg/atomic/issues/58  

Visiting https://www.boost.org/libs/atomic redirects to https://www.boost.org/doc/libs/1_79_0/doc/html/atomic.html which is 404:  
  
> 404 Not Found  
> File "/home/www/shared/archives/live/boost_1_79_0/doc/html/atomic.html" not found.  
> Unable to find file.  
  
The right link is: https://www.boost.org/doc/libs/1_79_0/libs/atomic/doc/html/index.html  
  
The root index.html currently redirects to "../../doc/html/atomic.html" but should redirect to "doc/html/index.html" instead.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-04-18 12:14:52 UTC  
> Url: https://github.com/boostorg/atomic/issues/58#issuecomment-1101352926  

Fixed, thanks. Could you update the docs on the website?

---

## Comment 2

> Username: glenfe  
> Created at: 2022-04-18 20:27:39 UTC  
> Url: https://github.com/boostorg/atomic/issues/58#issuecomment-1101734560  

Will do tonight.

---

## Comment 3

> Username: glenfe  
> Created at: 2022-04-19 01:25:35 UTC  
> Url: https://github.com/boostorg/atomic/issues/58#issuecomment-1101903258  

Fixed in 1.79 archives on live site.

---

## Comment 4

> Username: Lastique  
> Created at: 2022-04-19 08:17:12 UTC  
> Url: https://github.com/boostorg/atomic/issues/58#issuecomment-1102267273  

Thanks.
