# #45 [overlay] fix comparison between signed/unsigned integer expressions warning [Closed]

> Username: mkaravel  
> Created at: 2014-05-28 01:18:19 UTC  
> Updated at: 2014-05-28 12:32:32 UTC  
> Closed at: 2014-05-28 12:28:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/45  

warning produced with g++ with -Wall

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-05-28 11:38:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/45#issuecomment-44395452  

@barendgehrels I have done the change. I am still waiting to make sure the unit tests are okay.  
Please do not change anything.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-05-28 11:46:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/45#issuecomment-44396096  

@barendgehrels changed definition of minimum_ring_size and updated code in append_no_dups_or_spikes

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-05-28 11:51:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/45#issuecomment-44396501  

I see... hmm, if done like this, I could have merged it right from the beginning. It is also the history (change, another change on top of the first one reverting the first one) why I did not do it. Anyway, probably not really important. I think I will merge it.  
Thanks anyway.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-05-28 12:01:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/45#issuecomment-44397248  

OK in the end two int() cast were missed. I decided to do the changes (4 lines) separately. It is pushed now.  
So this does not need to be pulled - how can we disable it? The button is now gray automatically (good github).  
Despite not merged, thanks for your action, the result is as intended.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-05-28 12:32:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/45#issuecomment-44400031  

@barendgehrels Pull request closed. You do need to do anything more. Thanks for taking care of this.

---
