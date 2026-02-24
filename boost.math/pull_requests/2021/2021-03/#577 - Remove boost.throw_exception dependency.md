# #577 Remove boost.throw_exception dependency [Merged]

> Username: mborland  
> Created at: 2021-03-21 10:50:17 UTC  
> Updated at: 2021-03-23 20:41:15 UTC  
> Merged at: 2021-03-23 17:04:48 UTC  
> Closed at: 2021-03-23 17:04:48 UTC  
> Url: https://github.com/boostorg/math/pull/577  

Contains `#define BOOST_MATH_STANDALONE` so that the CI system will test standalone mode. Will remove before merge.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-03-21 12:08:44 UTC  
> Url: https://github.com/boostorg/math/pull/577#issuecomment-803566178  

Looks good, thanks Matt.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-03-23 15:46:01 UTC  
> Url: https://github.com/boostorg/math/pull/577#issuecomment-805011848  

This is clean, but I have one question before it goes in. For metal environments like @ckormanyos is using is it worth adding another option that is something to the affect of the following:  
  
```  
#if defined(BOOST_MATH_STANDALONE) && defined(BOOST_MATH_NO_THROW)  
#define BOOST_MATH_THROW_EXCEPTION(expr) std::abort() or std::exit(EXIT_FAILURE);  
#endif  
```

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-03-23 17:05:23 UTC  
> Url: https://github.com/boostorg/math/pull/577#issuecomment-805072787  

@mborland : Not sure, but certainly a separate pull request. . .

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-03-23 17:52:59 UTC  
> Url: https://github.com/boostorg/math/pull/577#issuecomment-805107421  

>Not sure, but certainly a separate pull request. . .  
  
+1.  Lets get some sort of standalone mode working and then I'm sure Chris will chime in with whatever else is required, everything is macro-ised so it's an easy change down the road.  Probably we should let the user define BOOST_MATH_THROW_EXCEPTION on the command line as one option?

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-03-23 20:41:15 UTC  
> Url: https://github.com/boostorg/math/pull/577#issuecomment-805237392  

> For metal environments like @ckormanyos is using is it worth adding another option that is something to the affect of the following  
  
Thank you for thinking of this. I think that no special provisions should be made for metal. Bare metal is a new concept that we will work on from time to time as we progress. The main flow of Boost.Math should, in my opinion, in no way be hindered or modified by this.  
  
> +1. Lets get some sort of standalone mode working and then I'm sure Chris will chime in with whatever else is required,  
  
Yes. I absolutely agree. Please proceed without any concern whatsoever about such matters. proceed please full speed ahead with what we would consider the normal and usual PC approach without any considerations for metal adaptions, which we can handle _after_ reaching stability.  
  
Kind regards, Chris

---
