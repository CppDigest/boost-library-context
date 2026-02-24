# #201 - Review static constexpr ODR-uses [Closed]

> Username: anarthal  
> Created at: 2023-12-20 17:27:17 UTC  
> Updated at: 2024-05-16 10:52:02 UTC  
> Closed at: 2024-05-16 10:52:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/201  

Following https://github.com/boostorg/system/issues/115, review that we're not affected, too.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-05-16 10:52:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/201#issuecomment-2114885913  

We're not affected since we only use plain template variables, and not `static constexpr` variables as in the linked issue, which are the ones requiring a definition.
