# #2 Add metadata. [Closed]

> Username: danieljames  
> Created at: 2014-02-27 23:01:33 UTC  
> Updated at: 2014-02-27 23:39:03 UTC  
> Closed at: 2014-02-27 23:39:03 UTC  
> Url: https://github.com/boostorg/assert/pull/2  

This adds the metadata I've been discussing on the list recently. I want to get it into assert so I can use it to add it to the library list on the site. Should it have a backdated `boost-version` field for an earlier version of boost? The field represents the first version of boost to contain the library.

---

## Comment 1

> Username: pdimov  
> Created_at: 2014-02-27 23:09:41 UTC  
> Url: https://github.com/boostorg/assert/pull/2#issuecomment-36303630  

Is master the right branch, or does it actually need to go into develop first? I always forget to set the target branch to develop when doing pull requests, so I thought I'd ask.  
  
No idea about boost-version. BOOST_ASSERT is old, but the actual module is new.

---

## Comment 2

> Username: danieljames  
> Created_at: 2014-02-27 23:21:00 UTC  
> Url: https://github.com/boostorg/assert/pull/2#issuecomment-36304498  

Sorry, it should have been develop. I can redo the pull request if you want. I'll look into the first version to have assert.

---

## Comment 3

> Username: pdimov  
> Created_at: 2014-02-27 23:33:03 UTC  
> Url: https://github.com/boostorg/assert/pull/2#issuecomment-36305388  

The first Boost version to have BOOST_ASSERT is 1.27.0, but it's undocumented and a bit different than today's. The first version to have it documented is 1.30.0.

---

## Comment 4

> Username: danieljames  
> Created_at: 2014-02-27 23:39:03 UTC  
> Url: https://github.com/boostorg/assert/pull/2#issuecomment-36305817  

New pull request at https://github.com/boostorg/assert/pull/3

---
