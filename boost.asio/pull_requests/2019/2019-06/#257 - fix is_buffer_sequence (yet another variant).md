# #257 fix is_buffer_sequence (yet another variant) [Closed]

> Username: karzhenkov  
> Created at: 2019-06-29 15:43:35 UTC  
> Updated at: 2019-07-01 15:14:53 UTC  
> Closed at: 2019-07-01 15:14:26 UTC  
> Url: https://github.com/boostorg/asio/pull/257  

Here is reworked variant of `is_buffer_sequence`. It is more concise than the original, since it does not introduce several helper declarations for each individual check. This variant solves the issue described in PR #218 and satisfies the test proposed in PR #253.  
  
Some additional changes may be also considered. Perhaps a bit more consistent approach is to check only return types of `buffer_sequence_begin` and `buffer_sequence_end` template functions and to exclude other checks (see 8cd7ba9b9e07171d4c1628b101469962bc330f81). This allows to use any class having corresponding specializations of these templates as a buffer sequence. Such a classes are, for example `mutable_buffer` and `const_buffer`. They would not need the individual specializations of `is_buffer_sequence` class template.  
  
However, the commit 8cd7ba9b9e07171d4c1628b101469962bc330f81 causes the test from PR #253 to fail with several "false positives". Probably the test needs to be revisited (relaxed).

---

## Comment 1

> Username: karzhenkov  
> Created_at: 2019-07-01 15:14:53 UTC  
> Url: https://github.com/boostorg/asio/pull/257#issuecomment-507307346  

Some improvements are needed

---
