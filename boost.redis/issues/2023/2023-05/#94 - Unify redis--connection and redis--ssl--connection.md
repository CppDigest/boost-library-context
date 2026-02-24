# #94 - Unify redis::connection and redis::ssl::connection [Closed]

> Username: mzimbres  
> Created at: 2023-05-03 21:40:22 UTC  
> Updated at: 2023-05-13 16:22:54 UTC  
> Closed at: 2023-05-13 16:22:54 UTC  
> Url: https://github.com/boostorg/redis/issues/94  

Having two different types for plain and ssl connection is only annoying and offer no benefit for end users. I have felt this pain myself when working with beast and have seem other users complaining about this.  
  
In other major languages clients will also decide palin vs ssl at runtime. So I believe this feature will reduce the barrier even further for adopting C++ for the web apps.
