# #92 - remove_prefix and remove_suffix overly permissive [Closed]

> Username: sehe  
> Created at: 2022-05-02 05:26:10 UTC  
> Updated at: 2022-07-06 00:14:43 UTC  
> Closed at: 2022-07-06 00:14:43 UTC  
> Url: https://github.com/boostorg/utility/issues/92  

The implementation contains extra logic:  
  
```c++  
BOOST_CXX14_CONSTEXPR void remove_prefix(size_type n) {  
   if ( n > len_ )  
       n = len_;  
```  
  
While the extra check for out-of-range argument value seems nice, it can hide problems. Worse, when eventually users will (have to) switch to other implementations, this will silently introduce UB into code that was unwittingly/wittingly relying on the non-standard behaviour.  
  
Suggest to replace the condition with a BOOST_ASSERT so the danger is removed.

---

## Comment 1

> Username: mclow  
> Created at: 2022-05-30 20:36:02 UTC  
> Url: https://github.com/boostorg/utility/issues/92#issuecomment-1141463500  

As I read your issue, I am thinking "you want to count on undefined behavior?" - because that's what you're asking for.

---

## Comment 2

> Username: mclow  
> Created at: 2022-05-30 20:36:43 UTC  
> Url: https://github.com/boostorg/utility/issues/92#issuecomment-1141463771  

Maybe a better comment is "You want consistent undefined behavior?"

---

## Comment 3

> Username: Lastique  
> Created at: 2022-05-30 21:55:17 UTC  
> Url: https://github.com/boostorg/utility/issues/92#issuecomment-1141498448  

I think the incentive is to be able to detect incorrect arguments (in debug builds) rather than silently accept them. In release builds let the UB take place.

---

## Comment 4

> Username: sehe  
> Created at: 2022-05-30 22:17:22 UTC  
> Url: https://github.com/boostorg/utility/issues/92#issuecomment-1141512797  

All in all, you got it. Let me reinforce re:  
  
> Maybe a better comment is "You want consistent undefined behavior?"  
  
Yes. Because I want to **avoid** the silent pitfall where people rely on this lenient behavior of a supposed standard-compliant `string_view` only to get spurious UB when they finally do move to `std::string_view` (or some other implementation).  
  
[Hyrum's Law](https://www.hyrumslaw.com/) with a twist

---

## Comment 5

> Username: mclow  
> Created at: 2022-05-30 22:19:29 UTC  
> Url: https://github.com/boostorg/utility/issues/92#issuecomment-1141513694  

There is no such thing as "consistent undefined behavior".  That's a fallacy.  If you want implementations to behave identically, then the behavior is *defined*.

---

## Comment 6

> Username: sehe  
> Created at: 2022-05-30 22:47:12 UTC  
> Updated at: 2022-05-30 22:48:47 UTC  
> Url: https://github.com/boostorg/utility/issues/92#issuecomment-1141523186  

@mclow It was _you_ planting the fallacy, and I happily obliged because it's not about that. I rather took it to mean you *got* what it *is* about.  
  
Some approaches open up the user to surprise, others do not. We can choose. If¹ you don't care, fine. I do.  
  
Meanwhile, Boost Utility doesn't document these functions at all (beyond listing their existence). People *will* go and check the implementation to see what guarantees are made.  
  
Going back to your initial comment ("_I am thinking "you want to count on undefined behavior?" - because that's what you're asking for._") can you see I'm merely trying to be realistic?  
  
¹ note the _if_

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-07-05 13:52:33 UTC  
> Url: https://github.com/boostorg/utility/issues/92#issuecomment-1175087317  

This whole debate is pointless, just look at Peter's implementation and see what he did if you want to know what is correct: https://github.com/boostorg/core/blob/ac9d79992e0f5bcdb8953330c67fe9c706c9f79f/include/boost/core/detail/string_view.hpp#L532  
  
seems like he doesn't perform the check.
