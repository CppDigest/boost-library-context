# #702 - Documentation link broken [Closed]

> Username: JLospinoso  
> Created at: 2017-07-30 17:48:33 UTC  
> Updated at: 2017-07-30 22:54:39 UTC  
> Closed at: 2017-07-30 22:54:39 UTC  
> Url: https://github.com/boostorg/beast/issues/702  

In README, documentation link is broken. I suspect this is to do with Boost not yet being updated on boost.org.  
  
### Version of Beast  
  
n/a -- this applies to master  
  
### Steps necessary to reproduce the problem  
  
Visit http://www.boost.org/doc/libs/master/libs/bind/doc/html/beast/quick_start.html  
  
### All relevant compiler information  
  
n/a

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-30 18:41:44 UTC  
> Url: https://github.com/boostorg/beast/issues/702#issuecomment-318920835  

That's weird, I could swear I just fixed it yesterday! Would you mind refreshing the repository page and trying again?

---

## Comment 2

> Username: JLospinoso  
> Created at: 2017-07-30 18:48:28 UTC  
> Url: https://github.com/boostorg/beast/issues/702#issuecomment-318921278  

Still getting a 404:  
  
404 Not Found  
  
File "/home/www/shared/archives/live/master/libs/bind/doc/html/beast/quick_start.html" not found.  
  
Unable to find file.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-30 19:06:26 UTC  
> Url: https://github.com/boostorg/beast/issues/702#issuecomment-318922448  

I see it now, it looks like I missed a spot, thanks!  
  
Please use this link for now, I will update the branch momentarily:  
http://vinniefalco.github.io/beast/beast/quick_start.html

---

## Comment 4

> Username: JLospinoso  
> Created at: 2017-07-30 19:14:40 UTC  
> Url: https://github.com/boostorg/beast/issues/702#issuecomment-318922944  

No problem, and congrats on getting your stellar library into Boost. Great work!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-30 19:35:17 UTC  
> Url: https://github.com/boostorg/beast/issues/702#issuecomment-318924100  

Thank you very much! If you're using Beast I would love to hear about what you're doing (just the parts you feel comfortable talking about). And if you have any problems, or what you think could be improved...any feedback you provide will help make the library better!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-30 22:54:39 UTC  
> Url: https://github.com/boostorg/beast/issues/702#issuecomment-318935769  

This is merged
