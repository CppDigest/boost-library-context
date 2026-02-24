# #348 - Support RESP2 NULL [Open]

> Username: anarthal  
> Created at: 2025-11-03 14:36:21 UTC  
> Updated at: 2025-12-21 11:38:45 UTC  
> Url: https://github.com/boostorg/redis/issues/348  

This is encoded as `*-1\r\n` or `$-1\r\n`. When we encounter this, we currently fail with `error::not_a_number`. I think we could support this to soften the RESP3 requirement (I'd say this is the only RESP2 feature we don't support).  
  
For the record, I've encountered this with SENTINEL, as it sends a NULL array if you forget to upgrade the connection to RESP3 with HELLO.

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-11-08 10:39:31 UTC  
> Url: https://github.com/boostorg/redis/issues/348#issuecomment-3506426819  

I agree it is simple enough but nobody ever asked for that yet. Do you think there is audience to justify the time invested? RESP3 started being supported in Redis 6 which is already 5 years old.

---

## Comment 2

> Username: kkzi  
> Created at: 2025-11-09 12:46:20 UTC  
> Url: https://github.com/boostorg/redis/issues/348#issuecomment-3508258138  

Some old systems are still using redis5 (RESP2), yes I am talking about our company’s projects

---

## Comment 3

> Username: anarthal  
> Created at: 2025-11-09 15:36:47 UTC  
> Url: https://github.com/boostorg/redis/issues/348#issuecomment-3508459587  

My idea was reducing possible footguns. We now have ways to avoid sending `HELLO` requests on startup, so encountering this is slightly more likely than before.  
  
@kkzi are you using (or plan on using) Boost.Redis at your company? I can prioritize this for the next release if that's the case.

---

## Comment 4

> Username: kkzi  
> Created at: 2025-12-21 09:55:23 UTC  
> Url: https://github.com/boostorg/redis/issues/348#issuecomment-3678650360  

> My idea was reducing possible footguns. We now have ways to avoid sending `HELLO` requests on startup, so encountering this is slightly more likely than before.  
>   
> [@kkzi](https://github.com/kkzi) are you using (or plan on using) Boost.Redis at your company? I can prioritize this for the next release if that's the case.  
  
Honestly, I'd totally use it; I'm a big fan of Boost! :-)  
  
Thing is, at my company, they're using hiredis to wrap stuff around it, and some projects are on swredis++. They're probably not hopping on the boost::redis bandwagon anytime soon.  
  
So, if you've got the time or are up for supporting the older version, that'd be awesome! I'd try to pitch it to the higher-ups. If not, no worries. Just me griping about us being stuck in the past with this tech stack.

---

## Comment 5

> Username: anarthal  
> Created at: 2025-12-21 11:38:45 UTC  
> Url: https://github.com/boostorg/redis/issues/348#issuecomment-3678716583  

Thanks for the input! I can spin an older Redis docker container and see how much of a hassle it is to support this, and then decide.
