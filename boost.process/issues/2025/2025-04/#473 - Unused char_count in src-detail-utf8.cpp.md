# #473 - Unused char_count in src/detail/utf8.cpp [Closed]

> Username: anarthal  
> Created at: 2025-04-04 14:49:18 UTC  
> Updated at: 2025-04-14 15:53:37 UTC  
> Closed at: 2025-04-14 15:53:37 UTC  
> Url: https://github.com/boostorg/process/issues/473  

In these lines: https://github.com/boostorg/process/blob/e637f8483da1d2ae2e4a33dc6732c9dbb198a297/src/detail/utf8.cpp#L208-L225  
  
`char_count` doesn't seem to be used anyhow. Compilers complain about it.
