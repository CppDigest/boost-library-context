# #253 Add test for "is_xxx_buffer_sequence" templates [Closed]

> Username: karzhenkov  
> Created at: 2019-06-10 17:08:10 UTC  
> Updated at: 2019-07-01 16:10:19 UTC  
> Closed at: 2019-07-01 16:10:19 UTC  
> Url: https://github.com/boostorg/asio/pull/253  

It seems the templates `is_mutable_buffer_sequence` and `is_const_buffer_sequence` are not working as intended. Proposed test will fail; the set of failed checks varies depending on the availability of `decltype`.  
  
The first test case is borrowed from PR #218; some other combinations of declarations in the inspected type are added. PR #218 fixes some failed checks, but not all.

---

## Comment 1

> Username: karzhenkov  
> Created_at: 2019-07-01 16:10:19 UTC  
> Url: https://github.com/boostorg/asio/pull/253#issuecomment-507329605  

Some improvements are needed

---
