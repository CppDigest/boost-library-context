# #11 Add missing size() member function to index_range; incorporate PR #1 [Closed]

> Username: CromwellEnage  
> Created at: 2018-05-07 10:26:27 UTC  
> Updated at: 2018-09-09 00:48:08 UTC  
> Closed at: 2018-09-09 00:48:08 UTC  
> Url: https://github.com/boostorg/multi_array/pull/11  

* The reference documentation specifies that index_range defines a size() member function, but the current implementation does not define it.  This PR rectifies the discrepancy.  
* Test programs rely on Boost.Core's lightweight_test facility vice Boost.Test.  
* Glen Fernandes' PR on improving allocator support <https://github.com/boostorg/multi_array/pull/1> in multi_array has been waiting a while for review.  Automated testing from the forked project has verified that his code works, so this PR includes it.  
* A few documentation links currently point to pages that HP retired along with the rest of the SGI STL website.  This PR points them to Robert Ramey's mirror.

---

## Comment 1

> Username: glenfe  
> Created_at: 2018-09-05 19:03:18 UTC  
> Url: https://github.com/boostorg/multi_array/pull/11#issuecomment-418845033  

> Glen Fernandes' PR on improving allocator support #1 in multi_array has been waiting a while for review. Automated testing from the forked project has verified that his code works, so this PR includes it.  
  
I dropped #1 and created #12. I had forgotten about it since it was a few years ago, but your mention here reminded me of it.

---

## Comment 2

> Username: glenfe  
> Created_at: 2018-09-09 00:48:08 UTC  
> Url: https://github.com/boostorg/multi_array/pull/11#issuecomment-419682647  

My new changes in #12 have been merged now so you can drop my old changes from your pull request. That said, this one pull request is too big in that it covers too many different and unrelated changes. You should split it up into multiple pull requests, so that Ron can evaluate each of the following requests independently:   
  
1. The change from Boost.Test to Lightweight_test  
2. The changes to documentation  
3. The configuration for Appveyor and Travis  
4. Adding the missing member function  
  
If you would like any help, let me know.

---
