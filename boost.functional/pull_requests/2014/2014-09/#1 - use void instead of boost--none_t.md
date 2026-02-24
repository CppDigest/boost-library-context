# #1 use void instead of boost::none_t [Closed]

> Username: akrzemi1  
> Created at: 2014-09-30 13:31:42 UTC  
> Updated at: 2015-01-10 13:36:13 UTC  
> Closed at: 2015-01-10 13:36:13 UTC  
> Url: https://github.com/boostorg/functional/pull/1  

This decouples Boost.Functional from Boost.Optional

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2014-12-19 14:52:31 UTC  
> Url: https://github.com/boostorg/functional/pull/1#issuecomment-67647223  

Anybody home?

---

## Comment 2

> Username: danieljames  
> Created_at: 2015-01-05 19:23:21 UTC  
> Url: https://github.com/boostorg/functional/pull/1#issuecomment-68760877  

The problem is that it's documented as using boost::none_t (someone might use the default in order to specify the third parameter), so this is technically a breaking change. IMO it would be better to move none_t into 'core' or something similar.

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2015-01-07 09:36:50 UTC  
> Url: https://github.com/boostorg/functional/pull/1#issuecomment-68998288  

I see what you mean. Let's not close this issue yet. Extracting `boost::none_t` to a separate library may also not work. It is too tightly connected to optional. For instance operator<< should work in a compatible way. I will need to rethink it.

---

## Comment 4

> Username: akrzemi1  
> Created_at: 2015-01-07 11:39:12 UTC  
> Url: https://github.com/boostorg/functional/pull/1#issuecomment-69011051  

Would you be OK with a two-phase removal? First, we introduce a second placeholder for a default type; still allow none_t, but make it deprecated. Second (say, after two releases), we remove the dependency?

---

## Comment 5

> Username: danieljames  
> Created_at: 2015-01-07 17:42:46 UTC  
> Url: https://github.com/boostorg/functional/pull/1#issuecomment-69059979  

If none_t must remain in optional, deprecating support for later removal is probably the best route. This will probably affect very few people, if any.

---

## Comment 6

> Username: danieljames  
> Created_at: 2015-01-10 13:17:24 UTC  
> Url: https://github.com/boostorg/functional/pull/1#issuecomment-69455396  

Change in: https://github.com/boostorg/functional/commit/188c8d4c5d0b6be36d6b74855a3d6a72e6c7f444  
  
Since this change isn't a massive problem, I made it immediately, but provided a backwards compatibility macro. That way if this does actually affect anyone, they'll find out sooner, rather than later, but will have an easy workaround if there's an actual problem. Possibly overkill.

---
