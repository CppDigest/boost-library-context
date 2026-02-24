# #31 - Document requirements on the user-defined `assertion_failed(_msg)` functions [Closed]

> Username: Lastique  
> Created at: 2023-04-23 21:59:28 UTC  
> Updated at: 2023-06-23 14:46:04 UTC  
> Closed at: 2023-06-23 14:46:04 UTC  
> Url: https://github.com/boostorg/assert/issues/31  

Currently, the documentation does not mention any requirements on the user-defined `assertion_failed(_msg)` functions beyond their signatures. However, at least the requirement that these functions should not return (normally) should be documented.  
  
One particular question I have is are these functions allowed to throw? This would affect whether `BOOST_ASSERT(_MSG)` can be used in `noexcept` functions, for example.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-06-22 15:51:23 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1602892890  

We don't place any requirements on the user-supplied handlers at the moment. They can return, and they can throw. In both cases, what happens afterwards is on the user; continuing after a failed assertion is unlikely to do anything useful, but that's not our problem. It's not going to be any worse than a disabled assertion due to NDEBUG.  
  
Throwing handlers are of similarly limited utility; the only legitimate use case we have for them is for precondition failure testing.

---

## Comment 2

> Username: Lastique  
> Created at: 2023-06-22 17:07:30 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1603024514  

Well, as I said in the description, the requirements (or lack of ones) on the assertion failure handlers places restriction on where `BOOST_ASSERT` is allowed to be used in our code. It would be useful to explicitly document the status quo and what it means for `BOOST_ASSERT` users - in particular, that it should not be used in `noexcept` functions unguarded. It didn't occur to me until recently, for example.  
  
> Throwing handlers are of similarly limited utility; the only legitimate use case we have for them is for precondition failure testing.  
  
Precondition and postcondition alike. I'd say, that's the *only* use of `BOOST_ASSERT`.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-06-22 18:18:59 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1603118124  

It can be used in noexcept functions unguarded.

---

## Comment 4

> Username: Lastique  
> Created at: 2023-06-22 20:58:20 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1603306062  

> It can be used in noexcept functions unguarded.  
  
How?

---

## Comment 5

> Username: pdimov  
> Created at: 2023-06-22 22:18:31 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1603383592  

`BOOST_ASSERT` is a replacement for `assert`. If you have `assert` in a `noexcept` function and the condition isn't true, it will call `abort`. If you replace this with `BOOST_ASSERT` and the user installs a throwing handler and the condition isn't true, it will call `terminate` which will call `abort`. It's not going to get any worse.

---

## Comment 6

> Username: Lastique  
> Created at: 2023-06-22 23:07:22 UTC  
> Updated at: 2023-06-23 14:38:53 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1603420349  

It *is* worse than `assert`:  
  
- If the user installed a throwing assert handler, and we implicitly allow such use, he is likely expecting that exception to propagate to some upper point where he can handle it. This expectation obviously breaks if there are `noexcept` calls on the way. Given that user's expectation is valid, this means `BOOST_ASSERT` should not be used in `noexcept` functions. (And if the expectation is wrong then why do we allow the handler to throw in the first place?)  
- In general, you may not know if there is a `noexcept` function higher up the stack, which means you may not know whether or not the program will terminate if `BOOST_ASSERT` throws. So, if throwing exceptions from `BOOST_ASSERT` is allowed, then the preceding code must be written with that in mind. That is, the code must be able to retain some level of correctness and recover from a failed `BOOST_ASSERT`. In particular, use RAII stuff more rigorously than would otherwise be needed. It also means `BOOST_ASSERT` cannot be used in code that simply must not fail *ever*.  
- Besides code correctness issues, terminating after an exception is not the same as terminating immediately. Exception propagation may change the program state and make subsequent debugging more difficult.  
- Compared to that, `assert` guarantees it does not throw and does not return if it fails. Which means none of the above is an issue.

---

## Comment 7

> Username: Lastique  
> Created at: 2023-06-22 23:12:32 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1603424163  

BTW, I can see plenty of uses of `BOOST_ASSERT` in `noexcept` functions in Boost. So it seems the most practical way forward is to require the failure handlers to not throw. And to not return for good measure, since returning makes even less sense than throwing.

---

## Comment 8

> Username: pdimov  
> Created at: 2023-06-23 12:38:08 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1604224074  

I don't think that any change here will be an improvement. I intend to keep using `BOOST_ASSERT` as originally intended, as a replacement for `assert`, which means in `noexcept` functions as well.  
  
At the same time, I see no reason to prohibit throwing user handlers. If the user wishes to install a throwing handler, that's his choice, and the consequences of throwing from `noexcept` are known.  
  
I don't believe that disallowing log and continue handlers - an even more legitimate choice than throwing - is also of any benefit to anyone. As I said, this isn't any worse than disabling asserts with NDEBUG.  
  
In SmartPtr, I remove the `noexcept` when there's a possibility for `BOOST_ASSERT` to throw: https://github.com/boostorg/smart_ptr/blob/5e7596ef06ffa7d27e1ae6806dfa48faafbaa884/include/boost/smart_ptr/detail/sp_noexcept.hpp#L32-L46  
  
But that's only because it's been requested by users, and not because this is the official policy. The official policy is, libraries can use `BOOST_ASSERT` wherever they would have used `assert`, and if the user installs a throwing handler or one that returns, the consequences are his responsibility. It's still better than not being able to install a handler at all.

---

## Comment 9

> Username: Lastique  
> Created at: 2023-06-23 13:16:35 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1604273163  

> In SmartPtr, I remove the `noexcept` when there's a possibility for `BOOST_ASSERT` to throw...  
> But that's only because it's been requested by users...  
  
Isn't that an indication that the problems I described are real, and something needs to be changed - either the requirements on assertion failure handlers or our policies re. `BOOST_ASSERT` usage?  
  
In any case, I still think these considerations need to be documented.

---

## Comment 10

> Username: pdimov  
> Created at: 2023-06-23 13:51:12 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1604315622  

The problem that `noexcept` and throwing on precondition violation don't mix well has been known for a while, and led to the adoption of the so-called "Lakos rule" in the standard library (that functions with preconditions can't be `noexcept`.)  
  
The rule has been controversial since its inception and there were some recent attempts to overthrow it, followed by further defense by John Lakos himself (https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2023/p2861r0.pdf) and others (https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2023/p2831r0.pdf, https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2023/p2834r0.pdf.)  
  
This has become even more relevant in light of the upcoming contracts. ("Log and continue" in the violation handler is also one of the use cases intended to be allowed by the contracts spec.)  
  
So yes, these things are real, but I don't think that anything needs to be changed in Boost.Assert.

---

## Comment 11

> Username: Lastique  
> Created at: 2023-06-23 14:40:31 UTC  
> Url: https://github.com/boostorg/assert/issues/31#issuecomment-1604380673  

Feel free to close this if you're not planning to do anything on this matter.
