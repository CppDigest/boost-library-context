# #14 Fix links to core documentation. [Closed]

> Username: danieljames  
> Created at: 2014-06-16 10:04:09 UTC  
> Updated at: 2014-06-16 11:42:57 UTC  
> Closed at: 2014-06-16 11:42:57 UTC  
> Url: https://github.com/boostorg/utility/pull/14  



---

## Comment 1

> Username: Lastique  
> Created_at: 2014-06-16 10:24:24 UTC  
> Url: https://github.com/boostorg/utility/pull/14#issuecomment-46162573  

Why are these changes needed? Core docs are built in core/doc/html, aren't they?

---

## Comment 2

> Username: danieljames  
> Created_at: 2014-06-16 10:42:35 UTC  
> Url: https://github.com/boostorg/utility/pull/14#issuecomment-46163955  

No. Look at:  
  
http://boost.cowic.de/rc/docs-inspect-develop.html#utility  
http://www.boost.org/doc/libs/master/libs/utility/  
http://www.boost.org/doc/libs/master/doc/html/core/addressof.html

---

## Comment 3

> Username: Lastique  
> Created_at: 2014-06-16 10:50:22 UTC  
> Url: https://github.com/boostorg/utility/pull/14#issuecomment-46164545  

Strange, when I type 'b2 release' in core/doc the docs are built in core/doc/html. What command is used to build the docs on the website?  
  
I'd prefer Core docs to be built in core/doc/html, if possible. Do you know how to do that?

---

## Comment 4

> Username: danieljames  
> Created_at: 2014-06-16 10:56:04 UTC  
> Url: https://github.com/boostorg/utility/pull/14#issuecomment-46165003  

It's built as part of the combined documentation, I assumed that was wanted as earlier documentation links were all set up to do that, for example:  
  
https://github.com/boostorg/core/blob/master/index.html

---

## Comment 5

> Username: Lastique  
> Created_at: 2014-06-16 11:06:22 UTC  
> Url: https://github.com/boostorg/utility/pull/14#issuecomment-46165734  

I can fix the links in index.html and swap.html. Is there anything else I can do to make the docs be built in core/doc/html?

---

## Comment 6

> Username: danieljames  
> Created_at: 2014-06-16 11:21:57 UTC  
> Url: https://github.com/boostorg/utility/pull/14#issuecomment-46166857  

I can add it to the documentation script so that it's built there, but please can you think about how the documentation is built earlier next time. I've had to make a lot of changes because of all the moving around of files. It would be too time consuming to chase people up every time, so I assume that whatever is checked in is what's wanted.

---

## Comment 7

> Username: Lastique  
> Created_at: 2014-06-16 11:27:23 UTC  
> Url: https://github.com/boostorg/utility/pull/14#issuecomment-46167248  

Ok, thanks. Maybe we should move all preferences from your scripts to the doc jamfiles instead of guessing each ones intentions?

---

## Comment 8

> Username: danieljames  
> Created_at: 2014-06-16 11:42:57 UTC  
> Url: https://github.com/boostorg/utility/pull/14#issuecomment-46168327  

The documentation build is run by a shell script, not a jamfile. It can't be done for a jamfile because the documentation is always built in the current working directory:  
  
https://svn.boost.org/trac/boost/ticket/3240  
  
Adding the script to the repo wouldn't help as I'd still be updating it myself, as no one else does these things.

---
