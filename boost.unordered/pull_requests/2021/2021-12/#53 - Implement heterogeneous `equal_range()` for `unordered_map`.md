# #53 Implement heterogeneous `equal_range()` for `unordered_map` [Merged]

> Username: cmazakas  
> Created at: 2021-12-01 18:07:42 UTC  
> Updated at: 2021-12-28 16:18:26 UTC  
> Merged at: 2021-12-02 19:19:28 UTC  
> Closed at: 2021-12-02 19:19:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/53  

Closes #49

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-12-02 02:25:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/53#issuecomment-984233831  

The test is incomplete. The map should have more than one element, and the test should check whether the returned range is of the correct length and whether the key is as expected (not just whether the value is 1337.)

---
