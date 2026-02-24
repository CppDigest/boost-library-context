# #19 - Symbol tables need better naming than `add()`/`insert()` [Closed]

> Username: tzlaine  
> Created at: 2023-12-02 22:09:40 UTC  
> Updated at: 2023-12-04 01:24:00 UTC  
> Closed at: 2023-12-04 01:24:00 UTC  
> Url: https://github.com/boostorg/parser/issues/19  

These are synonyms across languages for the `insert()` operation, and they don't convey that one of them (`add()`) is a permanently-mutating operation, and the other (`insert()`) is a parse-duration-only mutation.
