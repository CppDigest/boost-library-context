# #40 Fixing line_pos_iterator column numbering bug and get_current_line bug. [Closed]

> Username: tomoki  
> Created at: 2014-06-14 08:32:58 UTC  
> Updated at: 2017-12-01 14:02:04 UTC  
> Closed at: 2017-12-01 14:02:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/40  

This is cleaned up version of https://github.com/boostorg/spirit/pull/39.  
  
This pull request fixes following bugs.  
- line_pos_iterator's column numbering bug.  
- line_pos_iterator's get_current_line bug.  
  
And add regression test for line_pos_iterator.

---

## Comment 1

> Username: tomoki  
> Created_at: 2014-06-14 08:48:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/40#issuecomment-46082305  

Sorry, I didn't change JamFile and I don't know how to run it.  
Hold on.

---

## Comment 2

> Username: tomoki  
> Created_at: 2014-06-14 09:57:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/40#issuecomment-46083683  

I learned about jam and confirm that it successfully works.

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-06-16 04:25:11 UTC  
> Updated_at: 2014-06-16 04:45:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/40#discussion_r13791607  

The `BOOST_TEST` macro is the one that provides the least information, as it simply informs whether something does not hold. I recommend you use `BOOST_TEST_EQ` instead, which would print the value of the operands when the check does not hold. This would also mean splitting the compound first check into two independent checks. That way, if this test were to fail in one of the many machines running the regression tests, we would have as much information about the failures as possible.

---

## Comment 4

> Username: tomoki  
> Created_at: 2014-06-16 04:46:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/40#discussion_r13791813  

Thank you.I changed from all BOOST_TEST to BOOST_TEST_EQ.

---

## Review 5 [Commented]

> Username: octopus-prime  
> Created_at: 2017-11-30 16:16:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/40#pullrequestreview-80239252  

📁 test/support/regression_line_pos_iterator.cpp

```diff
  42 |+         }
  43 |+     }
  44 |+     return 0;
```

> Username: octopus-prime  
> Created_at: 2017-11-30 16:16:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/40#discussion_r154124142  

Should be `return boost::report_errors();` to report the errors.


---

## Comment 6

> Username: octopus-prime  
> Created_at: 2017-12-01 14:02:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/40#issuecomment-348502027  

See #305 and #304

---
