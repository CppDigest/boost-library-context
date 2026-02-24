# #1163 - Rescaling should have been removed in Boost 1.82 [Closed]

> Username: vissarion  
> Created at: 2023-06-19 09:35:22 UTC  
> Updated at: 2023-07-07 12:06:06 UTC  
> Closed at: 2023-07-07 12:06:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1163  

In `config.hpp` there is still a [message](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/core/config.hpp#L30) about deprecation of rescaling in 1.82.   
  
To my understanding the library is not ready to totally remove rescaling since there are a lot of tests depending on it (they fail without rescaling) and also a lot of issues. Maybe the message is about removing the macro `BOOST_GEOMETRY_USE_RESCALING` and replace it by `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE`. If this is the case we can simply remove the message at this step and then in the future work on removing rescaling functionality in general. @barendgehrels am I right?

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-06-19 19:02:45 UTC  
> Updated at: 2023-06-19 19:07:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1163#issuecomment-1597628630  

Hi @vissarion , good point. It should have been removed.  
  
At this moment, most tests that are marked as failing fail **with** rescaling. For example, in union, 7 tests fail with rescaling, against 1 which is invalid without. I didn't check all counts, but that was also my general idea.  
  
So the code *without* rescaling performs currently a lot better, which is why we changed the default. It is also reported, such as recently by Johan Dore in #1145  
  
Only incidentally it is still better for some cases, but now is the time to fix those (at least tried to), instead of recommending the macro.  
  
So we have the options:  
* remove rescaling now. That would be my choice. People have been warned, and they can stay at 1.82 if they still want rescaling  
* change the message to, for example, 1.84  
  
Replacing the macro would not be my choice, because current users (who use it) are used to this name. For me it makes no sense to just rename it.  
  
Being able to remove rescaling has the advantage that the code will become cleaner, it's not just the rescaling code itself, there is also some related code such as in get_clusters or sectionalize.  
  
But of course, there is an effort involved in removing it.

---

## Comment 2

> Username: vissarion  
> Created at: 2023-06-20 08:27:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/1163#issuecomment-1598342351  

Thanks @barendgehrels for the detailed reply. I agree that rescaling should be removed. I will try to do it for 1.83 (although the deadline is in one week unless we consider that change a bugfix and not a major change, then there is more time). In any case even if rescaling is not removed in 1.83 the message should be changed.   
  
>Replacing the macro would not be my choice, because current users (who use it) are used to this name. For me it makes no sense to just rename it.  
  
I also agree here, but I am a bit confused, what is the point of having `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` then? Should that be removed together with `BOOST_GEOMETRY_USE_RESCALING`?

---

## Comment 3

> Username: vissarion  
> Created at: 2023-06-20 09:00:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1163#issuecomment-1598388685  

Here is a list of issues that are affected by the removal of rescaling and (most probably) need to be fixed.  
https://github.com/boostorg/geometry/issues/1164  
https://github.com/boostorg/geometry/issues/1138  
https://github.com/boostorg/geometry/issues/1103  
https://github.com/boostorg/geometry/issues/1082  
https://github.com/boostorg/geometry/issues/1053  
https://github.com/boostorg/geometry/issues/1044  
https://github.com/boostorg/geometry/issues/1034  
https://github.com/boostorg/geometry/issues/1035  
  
  
and some old ones that have to be revisited:  
  
https://github.com/boostorg/geometry/issues/900  
https://github.com/boostorg/geometry/issues/893  
https://github.com/boostorg/geometry/issues/669  
https://github.com/boostorg/geometry/issues/663  
https://github.com/boostorg/geometry/issues/566

---

## Comment 4

> Username: barendgehrels  
> Created at: 2023-06-21 08:42:19 UTC  
> Updated at: 2023-06-21 08:49:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/1163#issuecomment-1600433975  

Thanks for this list! There are quite some new ones. I plan to visit them soon.  
The ones with integer (for example #1035) actually don't really belong to the list, because integer didn't use rescaling. But it might still be related, somehow.  
  
About `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE`: yes, I forgot that detail. I introduced it to make the switch easier. It should only be relevant for `.bjam` files. It selects what is *not* the default (so the alternative). Earlier it was *without* rescaling, but now it is *with* rescaling. It is not meant for the user, though currently it does the same thing indeed. Yes, it can be removed together.  
  
Finally, I would take more time to remove rescaling. It's a lot of work and a week is short. It's up to you of course, but I would not do it last moment. I mentioned `remove rescaling now` - but I didn't know there was only a week time...

---

## Comment 5

> Username: vissarion  
> Created at: 2023-06-21 10:36:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1163#issuecomment-1600598647  

Yes, indeed one week is short. Let's leave it for `1.84`.
