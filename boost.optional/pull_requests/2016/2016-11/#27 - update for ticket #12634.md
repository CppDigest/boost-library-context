# #27 update for ticket #12634: [Open]

> Username: akumta  
> Created at: 2016-11-30 02:37:02 UTC  
> Updated at: 2023-11-09 00:16:10 UTC  
> Url: https://github.com/boostorg/optional/pull/27  



---

## Comment 1

> Username: akrzemi1  
> Created_at: 2016-11-30 08:23:34 UTC  
> Url: https://github.com/boostorg/optional/pull/27#issuecomment-263811671  

I have put those guards, because Boost.Optional unit tests were failing on Sun OS compiler. Apparently it does not provide a full support for C++ language features.  
  
I would not consider it a good idea to enable Boost.Optional interface that relies on the features that are not implemented, or implemented incorrectly in a given compiler.  
  
Did you try to run the Boost.Optional unit tests after applying your fix locally? Do they pass?

---

## Comment 2

> Username: akumta  
> Created_at: 2016-11-30 16:52:08 UTC  
> Url: https://github.com/boostorg/optional/pull/27#issuecomment-263927604  

Let me run some more tests locally and perhaps put the guards around the latest compiler.

---
