# #5 Added BOOST_ASSERT_IS_VOID macro [Merged]

> Username: igaztanaga  
> Created at: 2015-07-10 19:44:12 UTC  
> Updated at: 2015-09-12 14:42:01 UTC  
> Merged at: 2015-09-12 14:42:01 UTC  
> Closed at: 2015-09-12 14:42:01 UTC  
> Url: https://github.com/boostorg/assert/pull/5  

Sometimes I need to write assertions that require some data preprocessing that should not be compiled/run when Boost.Assert is just a ((void)0) expression. This is a proposal to implement a macro that could help with these use cases. The name of the macro and the implementation are not important, this is just a proposal.

---

## Comment 1

> Username: pdimov  
> Created_at: 2015-07-21 21:05:01 UTC  
> Updated_at: 2015-07-23 08:55:45 UTC  
> Url: https://github.com/boostorg/assert/pull/5#discussion_r35154405  

Should be `BOOST_ASSERT_IS_VOID`, I guess?

---

## Comment 2

> Username: pdimov  
> Created_at: 2015-07-21 21:05:42 UTC  
> Updated_at: 2015-07-23 08:55:45 UTC  
> Url: https://github.com/boostorg/assert/pull/5#discussion_r35154466  

The text in `#error` looks wrong?

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2015-07-21 21:35:56 UTC  
> Updated_at: 2015-07-23 08:55:45 UTC  
> Url: https://github.com/boostorg/assert/pull/5#discussion_r35157632  

Yes, sorry.

---

## Comment 4

> Username: igaztanaga  
> Created_at: 2015-07-21 21:37:30 UTC  
> Updated_at: 2015-07-23 08:55:45 UTC  
> Url: https://github.com/boostorg/assert/pull/5#discussion_r35157780  

Right. It should read:  
  
"BOOST_ASSERT should NOT be void if NDEBUG is not defined"

---

## Comment 5

> Username: pdimov  
> Created_at: 2015-07-22 23:35:12 UTC  
> Url: https://github.com/boostorg/assert/pull/5#issuecomment-123906518  

Do you plan to update the PR with the fixes, or should I?

---

## Comment 6

> Username: igaztanaga  
> Created_at: 2015-09-10 19:18:04 UTC  
> Url: https://github.com/boostorg/assert/pull/5#issuecomment-139351896  

Hi Peter,  
  
Do you consider this ready to merge or maybe it's not useful enough?

---

## Comment 7

> Username: pdimov  
> Created_at: 2015-09-10 19:30:04 UTC  
> Url: https://github.com/boostorg/assert/pull/5#issuecomment-139354357  

I had missed your changes, sorry. I'll take a second look and merge.

---
