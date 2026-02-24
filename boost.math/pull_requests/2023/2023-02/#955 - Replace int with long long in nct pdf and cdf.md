# #955 Replace int with long long in nct pdf and cdf [Merged]

> Username: mborland  
> Created at: 2023-02-21 16:20:27 UTC  
> Updated at: 2023-02-23 16:21:58 UTC  
> Merged at: 2023-02-23 16:21:54 UTC  
> Closed at: 2023-02-23 16:21:54 UTC  
> Url: https://github.com/boostorg/math/pull/955  

See: https://github.com/scipy/scipy/issues/17916  
  
The upper bound for the non-centrality parameter is still lower than 2e16 from the reproducer, but it is much higher than it was. By extension the change also applies to non-central beta distribution.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-02-21 18:36:48 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1438932101  

Thanks Matt!

---

## Comment 2

> Username: NAThompson  
> Created_at: 2023-02-21 19:35:31 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1438993664  

FWIW, it wouldn't hurt to `git grep` for `itrunc` everywhere and see where it is sensible to replace with `lltrunc`; I've hit this issue before as well in a different context.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-02-22 12:30:38 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1439941797  

The failures in this PR should be fixed by https://github.com/boostorg/multiprecision/pull/536.  Sorry about that!

---

## Comment 4

> Username: mborland  
> Created_at: 2023-02-22 17:54:31 UTC  
> Updated_at: 2023-02-22 17:54:48 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1440508414  

@jzmaddock To buy us even more space I think we could conditionally use `__int128` instead of `long long` because k is never streamed. Do you think that would be worthwhile in the event you don't find a better solution in literature?

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2023-02-22 18:12:27 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1440541061  

> To buy us even more space I think we could conditionally use __int128 instead of long long because k is never streamed. Do you think that would be worthwhile in the event you don't find a better solution in literature?  
  
A quick bit of Googling isn't turning much up.  And yes I did wonder about using `__int128`, although we would have to check that a) it's available, and b) that the FP type has an explicit conversion to `__int128`.  I also wonder if we might be into a territory where we're calculating garbage given that ultimately we will have many __int128's mapping to single FP values once they get passed back to the incomplete beta?  
  
The other suggestion was to raise a domain_error in this case, and given that we know the limit on the non-centrality parameter this could be done early on in the distribution constructor?

---

## Comment 6

> Username: mborland  
> Created_at: 2023-02-22 18:16:31 UTC  
> Updated_at: 2023-02-22 18:17:29 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1440552033  

> The other suggestion was to raise a domain_error in this case, and given that we know the limit on the non-centrality parameter this could be done early on in the distribution constructor?  
  
For all of these it would be easy to wrap the call to `lltrunc` in a try block and then throw domain error in the event it fails.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2023-02-22 18:22:20 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1440561873  

Yup, although it circumvents the policy mechanism and building in exception-free environments, so maybe updating the checks for example here: https://github.com/boostorg/math/blob/2b9ab5a05fba1314cff97d06b73a4653331e2062/include/boost/math/distributions/non_central_t.hpp#L1095 might be better?

---

## Comment 8

> Username: mborland  
> Created_at: 2023-02-22 18:29:54 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1440575440  

> Yup, although it circumvents the policy mechanism and building in exception-free environments, so maybe updating the checks for example here:  
>   
> https://github.com/boostorg/math/blob/2b9ab5a05fba1314cff97d06b73a4653331e2062/include/boost/math/distributions/non_central_t.hpp#L1095  
>   
> might be better?  
  
Do we know the exact values for the nct failures because they are near, but not at the square? The other ones are straight-forward.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2023-02-22 18:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1440581952  

>Do we know the exact values for the nct failures because they are near, but not at the square? The other ones are straight-forward.  
  
Ah, good point.  Yes we need to be able to square the non-centrality, but in all cases we need to leave a bit of headroom as well, because we need to be able to iterate _forward_ from what is only a starting point.  So would `sqrt(max) - max_series_iteations` be reasonable?

---

## Comment 10

> Username: mborland  
> Created_at: 2023-02-22 18:42:57 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1440603430  

>   
> Ah, good point. Yes we need to be able to square the non-centrality, but in all cases we need to leave a bit of headroom as well, because we need to be able to iterate _forward_ from what is only a starting point. So would `sqrt(max) - max_series_iteations` be reasonable?  
  
I think that would work. Do you me to merge this so you can take over the error handling? I am very bad at working with the policies.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2023-02-22 18:51:06 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1440618128  

That's fine yes: BTW I'm still waiting for CI on multiprecision to cycle to fix those last few failures.  
  
Oh, and there's a boost release incoming.... once we're all green again we should probably do a merge to master and then get back to the bugs.

---

## Comment 12

> Username: mborland  
> Created_at: 2023-02-22 18:54:59 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1440624137  

Ok. Once I see the notification that you merged the multiprecision PR I will cycle the CI on this one to confirm the changes.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2023-02-23 09:52:30 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1441470086  

Cycling now...

---

## Comment 14

> Username: mborland  
> Created_at: 2023-02-23 16:21:44 UTC  
> Url: https://github.com/boostorg/math/pull/955#issuecomment-1442063475  

This is back to green so looks like your multiprecision fixes worked. Merging.

---
