# #836 X3: rollback iterator when rule's handler returns fail [Merged]

> Username: Xeverous  
> Created at: 2025-10-03 14:59:02 UTC  
> Updated at: 2025-10-06 11:36:43 UTC  
> Merged at: 2025-10-06 11:36:43 UTC  
> Closed at: 2025-10-06 11:36:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/836  

Test attempt to fix #833. First checking if I didn't broke anything

---

## Comment 1

> Username: saki7  
> Created_at: 2025-10-03 17:22:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/836#issuecomment-3366561206  

You can ignore the failures on AppVeyor. Our Windows CI is not properly configured since May 2025 and it has nothing to do with your PR. I'm not planning to fix this soon because all my CI renewals are reverted due to the C++23 revert.

---

## Comment 2

> Username: Xeverous  
> Created_at: 2025-10-03 18:39:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/836#issuecomment-3366821589  

Any opinion from you on the changes in code? The only implementation idea that came to my mind is to simply rollback the iterator when `fail` is returned from the handler (basically, turn `>` into `>>`).  
  
I noticed there are comments in `rule.hpp` and `guard.hpp` to keep them in sync. Even before my change, the code differed in regards to how it treats iterators. The guard implementation makes a copy of the iterator before parsing call (call with the copy) and only sets proper iterator if the parse succeeded.  
  
The rule implementation did not have this iterator copy and I wasn't sure whether:  
A) it should rollback only on fail (this is what I did in this patch)  
B) always rollback, except when parse succeeds (this is what guard implementation does)

---

## Comment 3

> Username: saki7  
> Created_at: 2025-10-04 02:54:47 UTC  
> Updated_at: 2025-10-04 02:55:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/836#issuecomment-3367794272  

You should sync the implementations of rule and guard. I think B is safer. You can consider this as the exception for "don't touch anything except for rule" I mentioned.

---

## Comment 4

> Username: Xeverous  
> Created_at: 2025-10-06 10:26:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/836#issuecomment-3370924996  

Adjusted the commit to solution B and added a description into the commit message.  
  
Unfortunately X3 has no `error_handler_result` tests that could be used to infer any particular design of iterator behavior.  
  
My intuition:  
- `error_handler_result::fail`: rollback iterator  
- `error_handler_result::retry`: (?) keep modified one (to retry in a different place), otherwise (unless the user changes some state) it will be applied forever in a loop; currently it rolls back  
- `error_handler_result::accept`: do not rollback - this is currently not implemented  
- `error_handler_result::rethrow`: with exceptions it does rethrow, without there is a TODO and I'm not really sure what should happen

---

## Comment 5

> Username: saki7  
> Created_at: 2025-10-06 11:33:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/836#issuecomment-3371182739  

The commit message looks very good, it addresses the investigation requirement I mentioned in https://github.com/boostorg/spirit/issues/833#issuecomment-3337339192.  
  
I'd rather choose to not touch the irrelevant enumerations other than `::fail`, thanks for the fix anyway.

---
