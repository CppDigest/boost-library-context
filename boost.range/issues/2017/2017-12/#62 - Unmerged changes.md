# #62 - Unmerged changes [Open]

> Username: pdimov  
> Created at: 2017-12-18 18:37:21 UTC  
> Updated at: 2019-05-05 11:23:17 UTC  
> Url: https://github.com/boostorg/range/issues/62  

The develop branch has a number of unmerged changes in addition to the fixes that have piled on top, and a maintainer needs to take a look at them and merge to master.

---

## Comment 1

> Username: neilgroves  
> Created at: 2017-12-19 10:35:43 UTC  
> Url: https://github.com/boostorg/range/issues/62#issuecomment-352704359  

I am the maintainer, and can merge these to master once the push_back issue is solved. The reason there is a backlog  of unmerged items is that there were a number of changes to address modularisation and decoupling that altered detail interfaces that I used in the implementations. The movement of headers and code due to the modularisation work meant that I could not easily merge my changes into master. I believe that my dependencies are now more consistent between develop and master. There are probably also tickets that I could have cherry-picked. My preferred procedure is to have develop working and discover a good version which I merge cleanly and simply to master.  
I see this sort of pile-up and delay in application, at least to some fraction of those commits that are outstanding, to be an inherent cost of the modularisation effort. Obviously I have the usual issue of never having as much spare time as I would have liked, otherwise I would have picked out the commits that could have been put onto master long ago.  
I will get the merge done to master when it is unfrozen.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-12-19 14:31:38 UTC  
> Url: https://github.com/boostorg/range/issues/62#issuecomment-352771602  

Thanks Neil.

---

## Comment 3

> Username: mclow  
> Created at: 2018-01-10 20:42:26 UTC  
> Url: https://github.com/boostorg/range/issues/62#issuecomment-356730166  

Looking forward to having `constexpr` `begin` and `end` on master.

---

## Comment 4

> Username: jeking3  
> Created at: 2019-05-05 11:23:17 UTC  
> Url: https://github.com/boostorg/range/issues/62#issuecomment-489417126  

What is the status of this issue?
