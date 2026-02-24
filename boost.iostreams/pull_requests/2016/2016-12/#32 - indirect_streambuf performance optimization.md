# #32 indirect_streambuf performance optimization [Closed]

> Username: yanikibo  
> Created at: 2016-12-06 19:43:30 UTC  
> Updated at: 2017-07-12 16:15:30 UTC  
> Closed at: 2017-07-12 16:15:30 UTC  
> Url: https://github.com/boostorg/iostreams/pull/32  

When the data is bigger than the stream buffer, we can suppress buffering and directly use the data for the io operation. Since the filter buffers are too small (default 128), usually we use bigger data, and the indirect_streambuf copies the data to these small buffers chunk by chunk before the io operation.  
Suppressing this unnecessary operation will give us a good performance improvement.

---

## Review 1 [Commented]

> Username: eldiener  
> Created_at: 2016-12-11 14:42:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iostreams/pull/32#pullrequestreview-12367809  

📁 include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp

```diff
 439 |+ 
 440 |+     if (avail != 0) {
 441 |+         traits_type::copy(s, gptr(), avail);
```

> Username: eldiener  
> Created_at: 2016-12-11 14:42:13 UTC  
> Url: https://github.com/boostorg/iostreams/pull/32#discussion_r91857151  

You are assuming here that 'avail' is <= to 'n'. I so no reason for this assumption.

> Username: eldiener  
> Created_at: 2016-12-11 15:03:07 UTC  
> Url: https://github.com/boostorg/iostreams/pull/32#discussion_r91857597  

OK, I see where you are checking the size at the beginning of the function.


---

## Comment 2

> Username: matbech  
> Created_at: 2017-06-04 04:46:08 UTC  
> Url: https://github.com/boostorg/iostreams/pull/32#issuecomment-306018038  

@yanikibo thank you for your contribution. I hope this will eventually get merged.

---

## Review 3 [Commented]

> Username: matbech  
> Created_at: 2017-06-13 11:48:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iostreams/pull/32#pullrequestreview-43697312  

wrong set_true_eof(true)

📁 include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp

```diff
 451 |+ 
 452 |+         if (len <= 0) {
 453 |+             this->set_true_eof(true);
```

> Username: matbech  
> Created_at: 2017-06-13 11:47:31 UTC  
> Updated_at: 2017-06-13 11:48:20 UTC  
> Url: https://github.com/boostorg/iostreams/pull/32#discussion_r121653441  

This is wrong. You should only set_true_eof(true) if ret == 0

> Username: yanikibo  
> Created_at: 2017-06-14 12:06:58 UTC  
> Updated_at: 2017-06-14 12:07:54 UTC  
> Url: https://github.com/boostorg/iostreams/pull/32#discussion_r121927391  

In IOStreams the devices return -1 for EOF. Really you can change it to len == -1. So this is correct.


---

## Comment 4

> Username: eldiener  
> Created_at: 2017-06-28 05:35:25 UTC  
> Url: https://github.com/boostorg/iostreams/pull/32#issuecomment-311561164  

I would like to get some specific additional iostream tests from you, as part of this PR, where the data is bigger than the stream buffer for the indirect_streambuf, in order to make sure that your xsgetn and xsputn are working properly in those cases. I am being cautious but since your PR is largely an optimization for iostreams I do not want to commit it unless I am absolutely certain based on tests that it is working properly.

---

## Comment 5

> Username: eldiener  
> Created_at: 2017-07-09 15:33:45 UTC  
> Url: https://github.com/boostorg/iostreams/pull/32#issuecomment-313926990  

I need specific tests that verify that the optimization code in this PR works correctly, else I am going to close this PR.

---
