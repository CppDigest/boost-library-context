# #301 Replaces tribool by an enum and adds coverage for multiplexer [Merged]

> Username: anarthal  
> Created at: 2025-09-06 19:04:59 UTC  
> Updated at: 2025-09-18 11:55:33 UTC  
> Merged at: 2025-09-18 11:55:29 UTC  
> Closed at: 2025-09-18 11:55:29 UTC  
> Url: https://github.com/boostorg/redis/pull/301  

* In the context of the multiplexer, replaces tribool by consume_result to enhance readability and make values smaller  
* Splits the multiplexer tests out of test_low_level_sync_sans_io into a separate test file  
* Increases testing coverage for the multiplexer class  
  
This PR entails no functional change.

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-09-06 19:06:28 UTC  
> Url: https://github.com/boostorg/redis/pull/301#issuecomment-3263049463  

My intention is fixing cancellation in `async_exec` next. This PR is intended to make sure that I don't break anything while doing it, since it entails changes to it. Also, it helps me get more familiar with the code. It's still in a draft state.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-13 16:02:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/301#pullrequestreview-3221058925  

📁 include/boost/redis/detail/multiplexer.hpp

```diff
  34 |- using tribool = std::optional<bool>;
  34 |+ // Return type of the multiplexer::consume_next function
  35 |+ enum class consume_result
```

> Username: mzimbres  
> Created_at: 2025-09-13 16:02:52 UTC  
> Url: https://github.com/boostorg/redis/pull/301#discussion_r2346808804  

This type could possibly be returned directly by the parser consume operation here https://github.com/boostorg/redis/blob/0cf2441ed2b81506f4b6f626d01ea43785ba31a9/include/boost/redis/resp3/parser.hpp#L80. But since this is hidden from the user at the moment we don't have to do it now.

> Username: anarthal  
> Created_at: 2025-09-15 17:25:19 UTC  
> Updated_at: 2025-09-15 17:25:21 UTC  
> Url: https://github.com/boostorg/redis/pull/301#discussion_r2349657454  

I will do it in subsequent PRs, since this is already pretty big.


---

## Comment 3

> Username: mzimbres  
> Created_at: 2025-09-13 16:09:22 UTC  
> Url: https://github.com/boostorg/redis/pull/301#issuecomment-3288581794  

Thanks, I had also thought in the past the tribool did not make the code very readable.

---

## Comment 4

> Username: anarthal  
> Created_at: 2025-09-16 14:40:04 UTC  
> Url: https://github.com/boostorg/redis/pull/301#issuecomment-3299091451  

I've added some more tests and refactored a little bit. Do you want to have a look before I merge @mzimbres ?

---
