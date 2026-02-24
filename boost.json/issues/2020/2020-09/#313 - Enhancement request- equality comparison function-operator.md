# #313 - Enhancement request: equality comparison function/operator [Closed]

> Username: hadrielk  
> Created at: 2020-09-11 13:08:48 UTC  
> Updated at: 2020-09-12 01:59:06 UTC  
> Closed at: 2020-09-11 19:09:18 UTC  
> Url: https://github.com/boostorg/json/issues/313  

I may be overlooking something, but I can't find a way to compare two `json::value`s. There's no `operator==()` for that type, nor any named function to perform a deep comparison. Correct?  
  
I think that's a fairly common requirement/use-case for this type of library, and was surprising to not have.  
  
Apologies if I just didn't see it. I'm still learning the API while testing it.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-11 13:40:29 UTC  
> Url: https://github.com/boostorg/json/issues/313#issuecomment-691101662  

That's right, there's no deep comparison, largely because objects are unordered. To perform the comparison would require allocating memory, which means it could throw. That is why `value` is _SemiRegular_, and not _Regular_. I have a function I use for testing which performs the comparison, it assumes that the order for objects is the same: https://github.com/CPPAlliance/json/blob/develop/test/test.hpp#L970  
  
Obviously, this is not general and relies on implementation details (which is OK for the tests). We might add a general version in the future, as an example program.

---

## Comment 2

> Username: hadrielk  
> Created at: 2020-09-11 13:54:29 UTC  
> Url: https://github.com/boostorg/json/issues/313#issuecomment-691109370  

Huh. What requires allocating memory for a deep comparison? The test function you cited doesn't appear to do so.(?)  
  
If that has to be done I get your point, but it feels really weird to me to not have comparison built into it - even if only via a named member or free function.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-11 13:58:28 UTC  
> Updated at: 2020-09-11 13:58:46 UTC  
> Url: https://github.com/boostorg/json/issues/313#issuecomment-691111434  

A library-quality equality function would have to either sort objects by key in order to perform the comparison (or else perform N^2 comparisons), since object ordering is undefined in JSON.

---

## Comment 4

> Username: hadrielk  
> Created at: 2020-09-11 14:23:06 UTC  
> Url: https://github.com/boostorg/json/issues/313#issuecomment-691124671  

Ahhh, I see the test function cheats - it assumes the ordering matches, which yeah clearly is not legit.  
  
I was assuming you could simply iterate over the left-hand-side's keys and do a `find(key)` on the right-hand-side. (and pre-check sizes are equal, etc.) Wouldn't that avoid having to create sorted views?  
  
Its complexity should be O(N) for the average case, but yeah N^2 for the worst case. But that's no different than `unordered_map`'s equality comparison complexity, afaik; and is likely what people would expect, as it's what other json libs do I believe.  
  
Sorry for belaboring this topic. I like the library a lot so far!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-09-11 14:34:40 UTC  
> Url: https://github.com/boostorg/json/issues/313#issuecomment-691131349  

> Sorry for belaboring this topic.  
  
No, you are doing 100% the right thing to be asking questions. Are you going to participate in the Boost formal review? It starts September 14th, just sign up on the mailing list:  
https://www.boost.org/community/groups.html#main  
  
Your feedback would be incredibly helpful, and you could bring up this point of the equality (which we will probably need to address).

---

## Comment 6

> Username: hadrielk  
> Created at: 2020-09-11 16:23:40 UTC  
> Url: https://github.com/boostorg/json/issues/313#issuecomment-691191313  

Yup, I'm on the mailing list and was planning to submit a review - it's how I got here. :)

---

## Comment 7

> Username: hadrielk  
> Created at: 2020-09-12 01:59:06 UTC  
> Url: https://github.com/boostorg/json/issues/313#issuecomment-691381250  

Oops, sorry for sending to the list - I thought that's what you wanted. I didn't notice you closed this with an actual fix. Thanks!
