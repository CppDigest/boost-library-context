# #175 This fixes the Visual Studio 2019 warning W26451 [Open]

> Username: FelixPetriconi  
> Created at: 2020-11-20 13:27:15 UTC  
> Updated at: 2022-02-12 02:15:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/175  

Please consider this small fix to avoid a Visual Studio warning.  
(Arithmetic overflow Using operator* on a 4 byte value and then casting the result to a 8 byte value.)

---

## Review 1 [Commented]

> Username: JeffGarland  
> Created_at: 2020-11-20 13:36:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/175#pullrequestreview-535432228  

probably a reasonable change -- would the impact be the same if we just changed the order to adjust*sub_sec so we wouldn't need the cast?  In any case let's see how the CI likes the change...

---

## Comment 2

> Username: FelixPetriconi  
> Created_at: 2020-11-20 21:44:00 UTC  
> Url: https://github.com/boostorg/date_time/pull/175#issuecomment-731422770  

As far as I can see the now failing builds already failed without my change. I will check your proposal of swapping the factors.

---

## Comment 3

> Username: jeking3  
> Created_at: 2022-02-12 02:15:55 UTC  
> Url: https://github.com/boostorg/date_time/pull/175#issuecomment-1036948460  

@FelixPetriconi rebase on develop for a complete CI scan

---
