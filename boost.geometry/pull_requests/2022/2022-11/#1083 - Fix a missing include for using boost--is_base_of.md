# #1083 Fix a missing include for using boost::is_base_of [Merged]

> Username: haubenmi  
> Created at: 2022-11-07 10:05:43 UTC  
> Updated at: 2022-11-16 13:13:14 UTC  
> Merged at: 2022-11-16 13:05:52 UTC  
> Closed at: 2022-11-16 13:05:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1083  



---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2022-11-07 13:52:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1083#pullrequestreview-1170439346  

Thanks for this fix. Would it be better to use the standard `std::is_base_of` and get ride of the dependency?

---

## Comment 2

> Username: haubenmi  
> Created_at: 2022-11-07 15:02:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1083#issuecomment-1305743511  

That will probably also work, I will update the pull request accordingly.

---

## Comment 3

> Username: haubenmi  
> Created_at: 2022-11-08 10:33:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1083#issuecomment-1306989452  

Sorry I need to put this pull request on hold until I get some legal approval from my employer, I hope it can wait until early next week?

---

## Comment 4

> Username: vissarion  
> Created_at: 2022-11-08 12:03:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1083#issuecomment-1307098083  

> Sorry I need to put this pull request on hold until I get some legal approval from my employer, I hope it can wait until early next week?  
  
Sure, no worries.

---

## Comment 5

> Username: haubenmi  
> Created_at: 2022-11-14 19:43:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1083#issuecomment-1314283398  

Hi @vissarion, I finally got everything sorted out, pushed a new version that uses std::is_base_of, and posted this PR for review.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2022-11-15 00:31:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1083#issuecomment-1314593582  

Thanks. Alternatively you could use `bg::util::is_multi<Geometry>::value` from [util/type_traits.hpp](https://github.com/boostorg/geometry/blob/0e961c6ad2f36e34aa71cf716210f4bc3990fd8f/include/boost/geometry/util/type_traits.hpp#L122) but this is fine too.

---

## Comment 7

> Username: haubenmi  
> Created_at: 2022-11-15 17:17:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1083#issuecomment-1315630893  

I'd also vote for keeping it like this. I just wanted to add that I cannot merge this PR, as I don't have write access, so one of you guys has to do it :)

---

## Review 8 [Approved]

> Username: barendgehrels  
> Created_at: 2022-11-16 09:32:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1083#pullrequestreview-1182208658  

Thanks!

---

## Comment 9

> Username: vissarion  
> Created_at: 2022-11-16 11:04:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1083#issuecomment-1316816581  

> I'd also vote for keeping it like this. I just wanted to add that I cannot merge this PR, as I don't have write access, so one of you guys has to do it :)  
  
Sure, I will merge your PR.

---
