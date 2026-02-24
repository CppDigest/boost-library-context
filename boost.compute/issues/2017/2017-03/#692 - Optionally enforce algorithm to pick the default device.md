# #692 - Optionally enforce algorithm to pick the default device [Closed]

> Username: keryell  
> Created at: 2017-03-13 17:14:08 UTC  
> Updated at: 2017-04-26 22:04:21 UTC  
> Closed at: 2017-04-26 15:03:52 UTC  
> Url: https://github.com/boostorg/compute/issues/692  

When using some weird devices or debugging OpenCL stacks & compilers, it does not make sense to run the application on anything else.  
  
If I correctly understand `find_default_device()`algorithm in https://github.com/boostorg/compute/blob/master/include/boost/compute/system.hpp#L210 on using the very handy`BOOST_COMPUTE_DEFAULT_` variables, when some criteria cannot be fulfilled we fall-back on returning the first GPU found, etc.  
  
So I'd like to add another variable environment for example (`BOOST_COMPUTE_DEFAULT_ENFORCE`? Bike-sheding welcome! :-) ) to change the selection algorithm to throw when a `BOOST_COMPUTE_DEFAULT_` variable is set but the criteria cannot be fulfilled.  
  
Any comment?  
  
I can write a PR.

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-03-18 13:38:36 UTC  
> Url: https://github.com/boostorg/compute/issues/692#issuecomment-287546933  

How throwing an exception would be helpful in such situations? I mean if you forget to set environment  variables so `find_default_device()` picks device you want to use, you can always forget about adding `BOOST_COMPUTE_DEFAULT_ENFORCE.

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-03-18 13:39:54 UTC  
> Updated at: 2017-03-18 13:40:54 UTC  
> Url: https://github.com/boostorg/compute/issues/692#issuecomment-287547011  

I mean, if it's not about forgetting to set environment variables, how otherwise would it be possible there is no device you want to use?

---

## Comment 3

> Username: kylelutz  
> Created at: 2017-03-21 03:04:50 UTC  
> Url: https://github.com/boostorg/compute/issues/692#issuecomment-287963408  

@keryell I think I understand what you're getting at. If you could submit a PR with the proposal that would be great! Thanks!

---

## Comment 4

> Username: jszuppe  
> Created at: 2017-04-20 19:44:12 UTC  
> Url: https://github.com/boostorg/compute/issues/692#issuecomment-295876783  

I'm waiting for that PR ;)

---

## Comment 5

> Username: keryell  
> Created at: 2017-04-21 09:59:00 UTC  
> Url: https://github.com/boostorg/compute/issues/692#issuecomment-296149179  

Yes I know. :-(  
But I am quite busy with a lot of deadlines. Such as the Khronos face-to-face meeting next week.  
Perhaps see you there?

---

## Comment 6

> Username: jszuppe  
> Created at: 2017-04-21 10:12:16 UTC  
> Url: https://github.com/boostorg/compute/issues/692#issuecomment-296152289  

> Yes I know. :-( But I am quite busy with a lot of deadlines.   
  
Sure :) I understand. Just remember it's assigned to you.  
  
> Such as the Khronos face-to-face meeting next week. Perhaps see you there?  
  
Maybe you'll hear me :), but it seems I won't be there in person.

---

## Comment 7

> Username: ddemidov  
> Created at: 2017-04-21 10:25:38 UTC  
> Url: https://github.com/boostorg/compute/issues/692#issuecomment-296154978  

Do you guys mean something like https://github.com/ddemidov/compute/commit/a7aa6cb564e3176d844c6fa4eb23cf448dcb502c?

---

## Comment 8

> Username: jszuppe  
> Created at: 2017-04-21 10:35:56 UTC  
> Url: https://github.com/boostorg/compute/issues/692#issuecomment-296156958  

Yes, I think so :) @keryell?

---

## Comment 9

> Username: jszuppe  
> Created at: 2017-04-26 15:03:52 UTC  
> Url: https://github.com/boostorg/compute/issues/692#issuecomment-297437720  

Implemented in https://github.com/boostorg/compute/issues/712.

---

## Comment 10

> Username: keryell  
> Created at: 2017-04-26 22:04:21 UTC  
> Url: https://github.com/boostorg/compute/issues/692#issuecomment-297554828  

Amazing!  
@ddemidov you are motivating me to procrastinate... :-)
