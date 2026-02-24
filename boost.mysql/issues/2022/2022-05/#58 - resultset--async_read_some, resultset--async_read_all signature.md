# #58 - resultset::async_read_some, resultset::async_read_all signature [Closed]

> Username: anarthal  
> Created at: 2022-05-11 09:37:25 UTC  
> Updated at: 2023-01-14 23:06:43 UTC  
> Closed at: 2023-01-14 23:06:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/58  

The current interface for `resultset::async_read_some` and `resultset::async_read_all` doesn't allow to reuse memory or custom allocators. This has been flagged during the review as a possible performance issue. Proposed solutions:  
  
a) Change the function signature to `async_read_all(vector<row, Allocator>&, CompletionToken&&)`, with the completion handler signature being `void(error_code)`.  
b) Remove the functionality altogether.  
c) Change the function signature to `async_read_all(RowOutputIterator, CompletionToken&&)`, with the completion handler signature being `void(error_code)`.  
  
If c) is chosen, then provide an example or a wrapper function allowing to read all the rows in a `resultset` into a `std::vector`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-05-15 14:15:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/58#issuecomment-1126950799  

Oh I like the sound of `RowOutputIterator`
