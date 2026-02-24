# #33 - A @note followed by a @see formats incorrectly [Closed]

> Username: vinniefalco  
> Created at: 2018-12-23 05:40:11 UTC  
> Updated at: 2020-11-10 01:55:09 UTC  
> Closed at: 2020-11-10 01:55:09 UTC  
> Url: https://github.com/boostorg/docca/issues/33  

Example, in a class javadoc  
```  
    ...  
  
    @note A timed stream object must not be moved or destroyed while there  
    are oustanding asynchronous operations associated with it. Objects of this  
    type meet the requirements of @b AsyncReadStream and @b AsyncWriteStream.  
  
    @see http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p1322r0.html  
*/  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-11-10 01:55:09 UTC  
> Url: https://github.com/boostorg/docca/issues/33#issuecomment-724398652  

I added a test case for this, and it looks fine.
