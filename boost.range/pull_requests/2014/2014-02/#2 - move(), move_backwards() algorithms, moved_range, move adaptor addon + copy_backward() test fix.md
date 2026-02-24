# #2 move(), move_backwards() algorithms, moved_range, move adaptor addon + copy_backward() test fix [Open]

> Username: awulkiew  
> Created at: 2014-02-20 02:20:43 UTC  
> Updated at: 2014-06-16 13:29:22 UTC  
> Url: https://github.com/boostorg/range/pull/2  

1. Boost.Range version of boost::move() and boost::move_backward() using Boost.Move implementations of these algorithms - boost::move() and boost::move_backward().  
2. moved_range wraps Boost.Move boost::move_iterator<>.  
3. added adaptors::move() and moved forwarder.  
4. Fixed error in copy_backward() test - wrong parameters were passed and wrong result was expected.

---

## Comment 1

> Username: neilgroves  
> Created_at: 2014-06-09 22:32:55 UTC  
> Url: https://github.com/boostorg/range/pull/2#issuecomment-45552888  

This looks like some cool functionality. Since there is renewed effort to modularise Boost I am proposing that the tokenized adaptor is moved to Boost.RegEx. I think that this adaptor should probably live in Boost.Move and therefore I am not going to merge this right now until the modularisation strategy is clear.  
  
It may be the case that we continue to put adaptors into Boost.Range at the cost of having numerous dependencies. If this turns out to be the case then I shall merge this straight in, otherwise I hope to use the code as a base for implementation that will go into Boost.Move (if the Boost.Move maintainer agrees).

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-06-14 16:58:09 UTC  
> Url: https://github.com/boostorg/range/pull/2#issuecomment-46093398  

For now, at least copy_backward() test fix could be cherry-picked.  
https://github.com/awulkiew/range/commit/8849b851bfdf63cd1e065633f5835ade44acaa8f

---

## Comment 3

> Username: neilgroves  
> Created_at: 2014-06-16 10:48:22 UTC  
> Url: https://github.com/boostorg/range/pull/2#issuecomment-46164381  

I am grateful that you highlighted the problems with the copy_backward unit test. Upon inspection I realised that I had not completed the unit test and that the coverage was insufficient. I have therefore improved coverage and fixed the mistakes rather than merge this change. This is pushed to the develop branch and after the tests cycle will appear on master ready for 1.56.

---
