# #179 - key_value_pair missing swap [Closed]

> Username: vinniefalco  
> Created at: 2020-08-20 20:07:31 UTC  
> Updated at: 2020-09-01 15:00:13 UTC  
> Closed at: 2020-09-01 15:00:12 UTC  
> Url: https://github.com/boostorg/json/issues/179  

Missing `swap` member and free function. I am struggling to see the use-case, this issue is here just so we don't forget.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-20 23:08:58 UTC  
> Url: https://github.com/boostorg/json/issues/179#issuecomment-677950034  

Not sure if it's "missing" anything. Sure `std::pair` has `swap`, but that's more general purpose than `key_value_pair`

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-21 01:25:36 UTC  
> Url: https://github.com/boostorg/json/issues/179#issuecomment-677986874  

we have it for all the other container types. not trying to argue for it, just pointing out facts.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-01 15:00:12 UTC  
> Url: https://github.com/boostorg/json/issues/179#issuecomment-684918683  

If a user complains, we can revisit.
