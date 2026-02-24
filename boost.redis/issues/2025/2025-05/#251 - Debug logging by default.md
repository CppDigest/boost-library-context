# #251 - Debug logging by default [Closed]

> Username: Chaosvex  
> Created at: 2025-05-17 06:40:05 UTC  
> Updated at: 2025-09-14 19:52:23 UTC  
> Closed at: 2025-09-14 19:52:23 UTC  
> Url: https://github.com/boostorg/redis/issues/251  

I've been going over the examples to start using the library and having debug logs hitting the console, intertwined with my existing logging, was a bit of a surprise.  
  
It took more searching to figure out how to disable it than it probably should as it doesn't appear to really be mentioned anywhere, although I did find the motivation for it (https://github.com/boostorg/redis/issues/219). Having to hunt around for how to disable it might have been down to much of the documentation on boost.org being broken, though.  
  
I think it'd reduce friction to have this behaviour be documented up front, although I'd disagree with the decision to enable it by default. Debug logging ought to be opt in, not opt out (IMO!).  
  
This is just my take as a newcomer to the library. No problem if I stand alone with this take, feel free to close the issue. 😀

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-05-17 14:59:44 UTC  
> Url: https://github.com/boostorg/redis/issues/251#issuecomment-2888442162  

Thanks for the feedback. I will add it to the docs soon.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-09-14 19:52:23 UTC  
> Url: https://github.com/boostorg/redis/issues/251#issuecomment-3289839276  

This is now documented here: https://www.boost.org/doc/libs/develop/libs/redis/doc/html/redis/logging.html
