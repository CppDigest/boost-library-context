# #651 X3: Fix multiple times missed force_attribute parameter [Merged]

> Username: Kojoley  
> Created at: 2021-02-21 17:03:16 UTC  
> Updated at: 2021-03-01 01:42:06 UTC  
> Merged at: 2021-03-01 01:42:05 UTC  
> Closed at: 2021-03-01 01:42:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/651  

Switched `parse_rule` to dispatch only on the rule id only instead.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2021-02-27 19:43:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/651#issuecomment-787125172  

@djowel can I get your opinion? Which fix do you prefer, https://github.com/Kojoley/spirit/commit/e4f1f5cacc2096dcbb9675e9e2f1c76be37b4958 (this PR) or https://github.com/Kojoley/spirit/commit/fadde9756f2e80e6715692a9fe12a9bc2260e3c5?

---

## Comment 2

> Username: djowel  
> Created_at: 2021-02-28 00:43:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/651#issuecomment-787210989  

> @djowel can I get your opinion? Which fix do you prefer, [Kojoley@e4f1f5c](https://github.com/Kojoley/spirit/commit/e4f1f5cacc2096dcbb9675e9e2f1c76be37b4958) (this PR) or [Kojoley@fadde97](https://github.com/Kojoley/spirit/commit/fadde9756f2e80e6715692a9fe12a9bc2260e3c5)?  
  
All things being equal, I think I prefer this one in terms of clarity of intent. Are there specific advantages of the other compared to this one?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2021-03-01 01:42:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/651#issuecomment-787577998  

I do not think there is any. I merge then.

---
