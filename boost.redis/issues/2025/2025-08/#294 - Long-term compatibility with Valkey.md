# #294 - [Question] Long-term compatibility with Valkey? [Closed]

> Username: bruno-viva  
> Created at: 2025-08-26 17:05:16 UTC  
> Updated at: 2025-09-01 18:14:27 UTC  
> Closed at: 2025-09-01 18:14:27 UTC  
> Url: https://github.com/boostorg/redis/issues/294  

Hi,  
  
Since Redis has forked and the Valkey project is gaining adoption (DigitalOcean, AWS, Google Cloud, etc. supporting it), I was wondering if there are any long-term plans for Boost.Redis to support Valkey as well.  
  
Specifically:  
  
- Is compatibility with Valkey an explicit design goal for the library, or is the focus strictly Redis?  
- Are there known differences that could impact Boost.Redis users if they connect to a Valkey instance?  
- Would contributions toward Valkey compatibility be welcomed if divergences appear in the future?  
  
It seems like Valkey is building momentum, so it’d be helpful to understand whether Boost.Redis aims to track both ecosystems.  
  
Thanks!

---

## Comment 1

> Username: anarthal  
> Created at: 2025-08-27 13:26:58 UTC  
> Url: https://github.com/boostorg/redis/issues/294#issuecomment-3228210428  

It looks like Valkey uses RESP3, so I'd be in favor of supporting it. It'd require us to add some extra CI builds. @mzimbres has the final word about this though.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-08-27 16:26:42 UTC  
> Url: https://github.com/boostorg/redis/issues/294#issuecomment-3228908486  

Marcelo has confirmed me via DM that we intend to support Valkey in the long run. We don't have CI infrastructure (yet, I'll take care of it in September), so I can't tell right now whethere there is any known difference between Valkey and Redis ATM. If Valkey implements RESP3 like Redis does, there shouldn't.  
  
We're open to contributions regarding this issue in the long run.

---

## Comment 3

> Username: bruno-viva  
> Created at: 2025-08-28 03:15:09 UTC  
> Url: https://github.com/boostorg/redis/issues/294#issuecomment-3231648648  

Sounds good, thanks all! I will switch from Redis to Valkey and report any issues I encounter (and I'll try to contribute with fixes, but I am still a newbie with boost code and can't promise I will succeed!).

---

## Comment 4

> Username: anarthal  
> Created at: 2025-08-28 06:25:05 UTC  
> Url: https://github.com/boostorg/redis/issues/294#issuecomment-3232090699  

Reporting issues is good enough. I can try to guide contributions too if they're simple enough. Thank you.

---

## Comment 5

> Username: anarthal  
> Created at: 2025-09-01 10:37:34 UTC  
> Url: https://github.com/boostorg/redis/issues/294#issuecomment-3241852462  

I've run our integration test suite and it looks like there is no compatibility issue with Valkey as far as we can detect. We're in the process of adding CIs to Valkey thus committing to support it in the long run.
